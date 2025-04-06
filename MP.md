# DQL编程控制

**DQL**（Data Query Language，数据查询语言）

## 构建查询条件

在进行查询的时候，我们的入口是在Wrapper这个类上，因为它是一个接口，所以我们需要去找它对应的实现类，关于实现类也有很多，说明我们有多种构建查询条件对象的方式，

![image-20241205105245770](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202412051055875.png)

### QueryWapper

```java
QueryWrapper qw = new QueryWrapper();
qw.lt("age",18);
List<User> userList = userDao.selectList(qw);
```

lt: 小于(<) ,最终的sql语句为

```sql
SELECT id,name,password,age,tel FROM user WHERE (age < ?)
```

在写条件的时候，容易出错，比如age写错，就会导致查询不成功

### QueryWapper基础上使用lambda

```java
QueryWrapper<User> qw = new QueryWrapper<User>();
qw.lambda().lt(User::getAge, 10);//添加条件
List<User> userList = userDao.selectList(qw);
```

- User::getAge,为lambda表达式中的，类名::方法名，最终的sql语句为:

```sql
SELECT id,name,password,age,tel FROM user WHERE (age < ?)
```

此时我们再次编写条件的时候，就不会存在写错名称的情况，但是qw后面多了一层lambda()调用

### LambdaQueryWrapper

```java
LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<User>();
lqw.lt(User::getAge, 10);
List<User> userList = userDao.selectList(lqw);
```

**注意:**构建LambdaQueryWrapper的时候泛型不能省。

LambdaQueryWrapper一种简化的方式：

```java
LambdaQueryWrapper<UserDO> queryWrapper = Wrappers.lambdaQuery(UserDO.class)
                .eq(UserDO::getUsername, username);
```

**`Wrappers.lambdaQuery(UserDO.class)`**：这是 MyBatis-Plus 提供的一个工具类 `Wrappers` 中的 `lambdaQuery` 方法。它简化了 `LambdaQueryWrapper` 的创建过程，可以直接通过 `Wrappers.lambdaQuery()` 来创建一个 `LambdaQueryWrapper` 对象。

`Wrappers.lambdaQuery(UserDO.class)` 是 MyBatis-Plus 中的一个工具方法，它帮助你更简洁地创建 `LambdaQueryWrapper` 实例。这样，你无需手动创建 `LambdaQueryWrapper` 的对象，而是通过这个工厂方法来生成实例。