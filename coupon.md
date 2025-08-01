# 后管系统

## 1.创建优惠券

流程：首先，通过责任链模式验证参数；然后，正常向数据库的优惠券表中插入这条优惠券的记录；然后，进行缓存预热，就是把刚刚插入到数据库中的数据存入`redis`中，这里采用Hash结构存储`redis.call('HMSET', KEYS[1], unpack(ARGV, 1, #ARGV - 1))`，过期时间就设置为这个优惠券的到期时间`redis.call('EXPIREAT', KEYS[1], ARGV[#ARGV])`。因为`redis`由于它是单线程模型，单条命令是原子性的，但它无法在设置Hash结构的值的时候同时设置过期时间，所以这里我用lua脚本执行，保证了设置值和过期时间两个操作的原子性。然后，用`RocketMQ5`之后的一个新特性，就是能够发送毫秒级别的任意延迟时间的延迟消息，在`springboot`中就是用redisTemplate的syncSendDeliverTimeMills方法。最后，把优惠券的id添加到布隆过滤器。

**责任链模式**：

主要解决了 **业务逻辑解耦** 的问题。

- 解耦：如果不使用责任链模式，会将所有校验逻辑挤在一个方法里，代码臃肿，新增或修改校验规则需要直接修改主逻辑。责任链模式可以将每个验证逻辑封装到一个独立的处理器中，每个处理器负责单一的验证职责，符合单一职责原则。
- 动态调整执行顺序：不同业务可能需要不同的校验顺序，例如：创建优惠券：先校验参数非空，再校验格式。删除优惠券：先校验权限，再校验是否存在。通过 `getOrder()` 动态调整顺序，无需硬编码。不同业务（`mark`）可以配置不同的处理器链。
- 可扩展性：如果新增一个“修改优惠券”功能，只需实现新 `Handler`，增加新的验证逻辑，添加新的处理器，而不需要修改现有的代码，。不影响旧逻辑，原有校验器可以复用（如参数非空校验）。

## 2.数据分片不均匀

![image-20250512222501069](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202505122225190.png)

如果采用上述这种ShardingSphere默认的哈希分片算法。那么会发生数据不均匀问题：

![image-20250512222618719](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202505122226854.png)

例如：先对店铺编号经过哈希，通过哈希值%2的操作确定属于哪个库。为偶数的哈希值%2后一定在0库，反之一定在1库。确定库后，通过哈希值%8的操作确定属于哪个表。由于0库中都是偶数的哈希值，那么一个大于8的值%8后一定都在偶数表中，反之1库中都在奇数表中。

根本问题是，分库这里没有把奇数偶数均匀分开。

- 自定义分片算法：

  - 分库：

    ![image-20250512223401621](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202505122234651.png)

  - 分表：

    ![image-20250512223458909](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202505122234939.png)

分库：例如，一个哈希值%16，得到0-15的一个数，再/8，得到0或1，此时不论这个哈希值是奇数还是偶数，都可能落在0或1库了。



## 3.商家创建优惠券分发任务

EasyExcel 解析百万数据量的 Excel 行数，仅避免了 JVM 内存占用过多问题。但还有一个问题：接口响应太慢，百万数据量需要解析 5 秒。下面通过线程池和 Redis 延迟队列的形式优化 EasyExcel 解析百万数据接口耗时问题，接口响应**从5 秒提升到毫秒级**。

### 3.1线程池异步解析 Excel 行数

创建一个公共线程池

```java
    private final ExecutorService executorService = new ThreadPoolExecutor(
            Runtime.getRuntime().availableProcessors(),
            Runtime.getRuntime().availableProcessors() << 1,
            60,
            TimeUnit.SECONDS,
            new SynchronousQueue<>(),
            new ThreadPoolExecutor.DiscardPolicy()
    );
```

`Executors` 工具类也可以直接创建线程池，这种是不推荐的。虽然 `Executors` 提供了创建线程池的便捷方法，然而，`Executors` 基于默认配置创建的线程池可能并不适合所有场景。

`newCachedThreadPool`：核心线程数为 0，使用同步的 `SynchronousQueue`，并且允许创建无限数量的线程。在高并发情况下，可能会创建大量线程，导致系统资源耗尽，甚至使系统崩溃。

1.`newFixedThreadPool(int nThreads)` —— 固定大小的线程池。创建一个固定大小的线程池，池中的线程数始终保持不变。当有新任务提交时，若线程池中的所有线程都在忙，则新任务会进入任务队列等待执行。

2.`newSingleThreadExecutor()` —— 单线程池。创建一个只有一个线程的线程池，所有任务按照提交顺序执行（FIFO、LIFO、优先级）

> 问题：`newFixedThreadPool` 和 `newSingleThreadExecutor`：这两种固定大小的线程池使用无界的 `LinkedBlockingQueue` 作为工作队列。当任务提交速度超过处理速度时，工作队列会不断增长，可能导致内存溢出。

3.`newScheduledThreadPool(int corePoolSize)` —— 单线程池。创建一个线程池，可用于执行定时任务或周期性任务。

> 问题：虽然最大线程数是 `Integer` 最大值，但是因为阻塞队列是无界的，所以核心问题同上。

4.`newCachedThreadPool()` —— 可缓存线程池。创建一个可以根据需要创建新线程的线程池，如果有空闲线程可用，就会重用空闲线程。如果某个线程在 60 秒内没有被使用，就会被回收。

> 问题：核心线程数为 0，使用同步的 `SynchronousQueue`，并且允许创建无限数量的线程。在高并发情况下，可能会创建大量线程，导致系统资源耗尽，甚至使系统崩溃。

参数：

workQueue：`SynchronousQueue` 是一种无缓冲队列，它的特点是生产者线程提交任务时，必须等待有消费者线程来取走任务，否则无法继续提交。在线程池中，`SynchronousQueue` 作为任务队列时，意味着线程池不会缓存任何任务，如果没有空闲线程可以立即执行任务，新的任务就会被拒绝或者导致新线程的创建。理论上不会有阻塞的情况，因为设置的线程数不少，所以这里使用不存储任务的同步队列。

handler：`ThreadPoolExecutor.DiscardPolicy` 是 Java 线程池中的一种拒绝策略，当线程池无法接收新的任务时，它直接丢弃新提交的任务，不会抛出异常，也不会进行任何日志记录。因为下面还有延时队列兜底，所以如果线程数都在运行，直接将任务丢弃即可。

### 3.2Redis延时队列兜底任务

当任务刚投递到线程池，还没有运行完，应用宕机了。就需要延时队列兜底，避免这种宕机行为。

## 4.优惠券分发失败记录深分页优化

优惠券分发失败记录可以通过后台分页查询，或者在分发完成后将失败记录写入 Excel 供运营人员查看。显然，后者更为优越，因为这些失败记录存储在数据库中价值不大，将其保存到 Excel 文件中不仅方便查看，还能节省数据库的存储空间。这种错误分发的情况不会很多。但只要是人为操作就会有漏洞（例如未检查优惠券模板的库存，或者数据库中提取的用户数据有误（用户已领取过优惠券但还是提取到了分发excel中））。并且随着商家越来越多，这种错误分发的情况、错误分发数量也会变的越来越多。因此，在这种极端情况下，需要通过分页的情况 limit offset, count 的形式进行按批次读取，例如下述 SQL：

```sql
select * from t_coupon_task_fail where batch_id = 'xxxxxx' limit 0, 1000;
```

这时，就会出现深分页的问题。

MySQL 的深分页问题指的是在使用分页查询时，随着页数的增加，查询的效率会显著降低，尤其是对于大量数据的表。这通常发生在使用 `LIMIT` 子句进行分页时，例如 `LIMIT offset, count`。当 `offset` 值很大时，MySQL 需要扫描和跳过大量的行，这会导致查询变慢。

对于 `SELECT * FROM table LIMIT 1000000, 10` 这样的查询，MySQL 会扫描前 1000000 条记录，然后丢弃它们，只返回接下来的 10 条。这意味着即使最终结果只包含 10 条记录，MySQL 仍然需要处理并丢弃大量的行，导致查询时间随着 `offset` 增大而增加。

解决方案：

1.子查询优化

```sql
select * from t_coupon_task_fail where id >=
    ->
    -> (SELECT id
    ->     FROM t_coupon_task_fail
    ->     WHERE batch_id = '1830889785603571980'
    ->     ORDER BY id
    ->     LIMIT 4999990, 1
    -> )
    ->
    -> limit 10;
```

因为子查询用到了索引。在索引上用聚集索引的主键 ID 进行查询省去了回表操作。

2.延迟关联

```sql
SELECT
    ->     t1.id,
    ->     t1.batch_id,
    ->     t1.json_object
    -> FROM
    ->     t_coupon_task_fail t1
    ->     INNER JOIN (
    ->         SELECT
    ->             id
    ->         FROM
    ->             t_coupon_task_fail
    ->         WHERE
    ->             batch_id = '1830889785603571980'
    ->         ORDER BY
    ->             id
    ->         LIMIT 4999990,
    ->         10) AS t2 on t1.id = t2.id
    -> LIMIT 10;
```

性能优化思路与子查询基本一致，只不过采用了 JOIN 的形式执行 SQL。

上述1.2.随着数据量的增多，性能也会下降。

3.书签记录

从原理上说，属于是一种滚动查询。也就是说我们必须从第一页开始查询，然后获取本页最大的记录 ID，然后再根据大于最大记录 ID 的数据向后持续滚动。也就是说，我们如果想查询大于 4999990 后记录的 10 条，那我们就得知道 4999990 条的 ID。

```sql
SELECT
    ->     *
    -> FROM
    ->     t_coupon_task_fail
    -> WHERE
    ->     batch_id = '1830889785603571980'
    ->     AND id > 4999990
    -> LIMIT 10;
```

查询 4000 万记录，性能依旧是微妙级，基本上可以认定这个性能是恒定的。

但是这个有点不好的地方在于，不支持跳页，只能上一页和下一页。不过对我们系统来说无所谓，因为我们反正是读取所有失败分发的记录生成 Excel ，本来就是要从头一直读到尾。

## 5.基于注解实现去重表消息防止重复消费（消息幂等性）

 消息中间件是一个可靠的组件，这里的可靠性指的是，只要消息被成功投递到了消息中间件，它就不会丢失，至少能够被消费者成功消费一次。这是消息中间件最基本的特性之一。即**“AT LEAST ONCE”**，即消息至少会被成功消费一遍。

例如：一个消息 M 被发送到消息中间件并被消费程序 A 接收到，A 开始消费这个消息，但是在消费过程中程序重启了。由于这个消息没有被标记为已经被消费成功，消息中间件会持续地将这个消息投递给消费者，直到消息被成功消费为止。

但是，这种可靠性会导致出现消息被多次投递的情况。例如：如果消费程序 A 接收并完成消息 M 的消费逻辑后，正准备通知消息中间件“我已经消费成功了”，但在此之前程序A又重启了，那么对于消息中间件来说，这个消息 M 并没有被成功消费过，因此消息中间件会继续投递这个消息。这样就会造成重复消费。

在 RockectMQ 的场景中，这意味着同一个 messageId 的消息会被重复投递。由于消息的可靠投递是更重要的，所以避免消息重复投递的任务转移给了应用程序自身来实现。这也是 RocketMQ 文档强调消费逻辑需要自行实现幂等性的原因。实际上，这背后的逻辑是：在分布式场景下，保证消息不丢和避免消息重复投递是矛盾的，但是消息重复投递是可以解决的，而消息丢失则非常麻烦。

解决方案：

使用 Redis 消息去重表，不依赖事务，针对消息表本身做了状态的区分：消费中、消费完成。

通过AOP，在执行消息消费之前，设置Redis中对应key 的 value值为 消费中，同时设置2分钟过期时间。

> 给初始化的幂等标识设置 2 分钟过期时间？
>
> 如果没有设置，那么当消息因某些异常原因（例如机器重启或外部异常）未成功消费，因为消息队列没有收到消费成功的消息，就会不断重新投递消息，但是接下来的每次查询都会显示消费中的状态，那么过一会就再投，一直重复，直到最终被视为消费失败并被投递到死信 Topic 中（RocketMQ 默认最多可以重复消费 16 次）。
>
> 那么，这里设置一个适中的过期时间，在消息队列不断重试投递的过程中，当过期时间到了后，就可以重新消费该消息了。

消息执行之后，设置Redis中对应key 的 value值为 已消费，同时设置2分钟过期时间。

## 6.兑换/秒杀优惠券功能

一般情况流程：

1.验证：

​	1.1先查询优惠券模版。保证优惠券在数据库中存在，且在缓存中存在。

​	1.2验证优惠券是否在活动有效期

2.通过lua脚本操作redis，扣减缓存中的优惠券库存，往缓存增加用户领券次数的记录。

3.开启编程式事务：

​	3.1通过Mysql的行记录锁扣减数据库中的优惠券库存

​	3.2往数据库的用户优惠券表中添加记录

​	3.3往Redis缓存中添加用户领取优惠券记录

​	3.4查询 Redis中用户领券记录是否持久化成功

> 在第3.4步中，由于Redis数据不一定持久化（RDB、AOF）成功，或者主从同步失败。那么在第3.4步之后，再查一下该缓存，如果没查到，那么可能是主从同步丢失了数据（因为一般是从节点读数据，主节点写数据）。那就再执行一次第3.4步操作。（这是 **先写再查**。先第3.4步写 再查一下）

​	3.5发送MQ延时消息，等优惠券到期后删除缓存记录。

问题1：

在这样一个高并发的场景下，第3步一个事务中操作了这么多 Redis 和 RocketMQ，就会导致事务时间延长以及接口响应速度变慢等问题。

解决1：

可以发现：3.3、3.4、3.5步骤逻辑都是在数据库操作成功的基础上执行的，那我们就可以通过 Canal监听Binlog 机制，异步执行这些逻辑就好了，这样就能不占用主逻辑的事务和响应时间了。当数据库发生变更操作时（比如，DML 语句（`INSERT`、`UPDATE`、`DELETE`），DDL 语句（`CREATE TABLE`、`ALTER TABLE`）等），就会记录到Binlog。然后，在Canal配置文件中配置好MQ相关配置后，当binlog发生变更时，Canal就会将这条变更消息发送到MQ消息队列等待消费了。

问题2：

主流程的操作（比如，步骤3.1、3.2依旧在主流程里）是同步执行的，导致响应时间变长，吞吐量下降。

解决2：

直接将步骤3发送给MQ，从而实现异步解耦。此时，因为步骤3整段逻辑已经通过消息队列异步解耦，就不用通过 Canal 进行订阅消费了。

上述解决2方案存在的问题：

1.redis极端场景

​	1.1持久化：RDB 快照和 AOF 日志文件追加。针对持久化的问题，可以通过3.4步骤 先写再查 尽可能增加成功率。

> RDB 它会根据情况定期的 Fork（复制） 出一个子进程，生成当前数据库的全量快照。对于 RDB 快照，假如我们在 RDB 快照生成后宕机，那么会丢失快照生成期间全部增量数据，如果在连快照都没成功生成，那么就会丢掉全部数据。
>
> 另一个是 AOF，它通过向 AOF 日志文件追加每一条执行过的指令实现。而当我们仅开启了 AOF 时，丢失数据的多少取决于我们设置的刷盘策略：当设置为每条指令执行后都刷盘 `Always`，我们最多丢失一条指令；当设置为每秒刷一次盘的 `Eversec` 时，最多丢失一秒内的数据；当设置为非主动刷盘的 `No` 时，则可能丢失上次刷盘后到现在的全部数据。

​	1.2主从复制：Redis 默认使用异步复制方式将主节点的数据传递给从节点。这意味着在主节点成功写入数据后，可能会因为从节点没有及时接收到该数据而造成数据丢失。特别是在主节点崩溃或重启时，如果从节点尚未完成数据同步，最新的数据将会丢失。

​	例如：假设当前 Redis 某个优惠券模板库存为 1。当张三用户兑换优惠券，Redis 扣减返回成功，这时 Redis 库存应该是 0，然后程序开始发送 RocketMQ 消息队列执行后续保存数据库流程。很不巧，虽然 Redis 返回了成功，但持久化失败（使得redis宕机后没有办法恢复持久化失败而丢失的数据）或者 Redis 主节点宕机（使得主从复制失败），造成这个记录并没有真正意义上执行完成。这个时候李四来兑换优惠券，发现 Redis 优惠券模板还是 1，扣减然后发送 RocketMQ。截止目前，张三和李四的优惠券兑换都是返回成功的。但是 RocketMQ 消费者消费时只有一个能成功，这就造成了问题，李四在使用的时候发现优惠券用不了，因为受消费者库存校验，并没有将优惠券记录新增到数据库中。该方案造成的影响就是用户看到返回优惠券成功，但是实际上没有办法使用。

2库存扣减的几种场景

​	2.1 在商品流量较低的情况下，通常不会出现大量请求同时访问单个商品进行库存扣减。此时，可以使用 Redis 进行防护，并直接同步到 MySQL 进行库存扣减，以防止商品超卖。虽然在此场景中涉及多个商品的数据扣减，可能会出现锁竞争，但竞争程度通常不会很激烈。

​	2.2对于秒杀商品，通常会在短时间内出现大量请求同时访问单个商品进行库存扣减。为此，也可以使用 Redis 进行防护，并直接将库存扣减同步到 MySQL，以防止商品超卖。由于秒杀商品的库存一般较少，因此造成的锁竞争相对可控。假设库存扣减采用串行方式，每次扣减耗时 5 毫秒，处理 100 个库存也仅需 500 毫秒。

​	2.3某些秒杀商品的库存较多，或同时进行多个热门商品的秒杀（如直播间商品）。在这种情况下，直接扣减数据库库存会给系统带来较大压力，导致接口响应延迟。为应对这种场景，我们设计了优惠券秒杀 v2 接口（即解决2方案）。虽然基于 Redis 扣减库存和消息队列异步处理的方案可能会引发前后不一致的问题，但它能显著提升性能。此外，Redis 的持久化和主从宕机的风险相对较小。即使发生宕机，对平台或商家来说，也不会造成直接的损失。

> 这里想强调的是，不存在绝对的银弹。Redis 之所以能快速响应，是因为它直接与内存交互，作为缓存中间件，如果每次都为了数据一致性而与磁盘交互，那就本末倒置了。我研究了市场上的云 Redis，包括腾讯 Redis 和阿里云 Tair，发现它们的持久化和主从复制本质上都是异步的。

![image-20250401223406415](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504012234530.png)

![image-20250401224151495](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504012241574.png)

## 7.优惠券预约通知功能

流程：

1.验证：

​	1.1先查询优惠券模版。保证优惠券存在在数据库中，且在缓存中存在。

​	1.2根据查询到的优惠券，判断它的开始时间是否在当前时间之前。如果是，则优惠券已经开始了，不能再预约了。

2.查询用户是否已经预约过该优惠券的提醒

​	2.1如果没有：设置提醒。

​	2.2否则：判断是否约过该时间的提醒。如果有，那就抛异常。否则，修改预约信息。

3.发送MQ延时消息

4.消费者消费消息

​	4.1先通过布隆过滤器判断是否已经取消了提醒

​	4.2通过 Redis 发送延时队列兜底（防止消息丢给线程池执行时突然断电）

​	4.3线程池执行消息

# 问题

