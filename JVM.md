# 如何判断对象是否可以被回收？

## 1.引用计数法

每个对象会维护一个引用计数器，记录有多少引用指向它。

当一个对象被某个变量引用一次，就让这个对象的计数+1，再被一个变量引用一次，计数就变为2。

当某个变量不再引用该对象，就让计数-1。

> 变量是在栈帧中，后面所引用的对象在堆中。
>
> 比如：List<Object> list = new ArrayList<>();
>
> list在栈帧中，new出来的对象在堆中。 

当计数器变为0时，意味着没人变量再引用它，那么它可以被作为垃圾而被回收。

弊端：循环引用

![image-20250430111150571](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504301111707.png)

早期的python虚拟机在进行垃圾回收时会使用引用计数法。java虚拟机没有采用这种方法，而是采用可达性分析法。

## 2.可达性分析算法

首先，会确定一系列根对象。

> 根对象：肯定不能被当作垃圾被回收的对象

在垃圾回收之前，会对堆内存中的所有对象进行一遍扫描，查看每个对象是否被根对象直接或间接引用。若是，则该对象不能被回收。反之，若不是，那么可以被作为垃圾将来被回收。

- 哪些对象可以作为GC Root对象？

  - System Class：由启动类加载器加载的一些核心的系统类，比如Object类、String类等。

  - Native Stack：JVM在执行一些方法调用时，需要调用操作系统的方法，操作系统方法在执行时要引用一些java对象，这些也是根对象。

  - Busy Monitor：sychronized中的锁对象不能被垃圾回收。即Monitor对象。

  - Thread：活动线程的栈帧内，局部变量所引用的对象可以作为根对象。

    > 比如：List<Object> list = new ArrayList<>(); 中这个ArrayList对象是根对象。
    >
    > 而当list = null后，没有变量再引用它，它就不再是根对象，可以被回收了。

## 3.五种引用

![](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504301428744.png)

实线：表示强引用

虚线：软弱虚终结期引用

### 3.1强引用

- 我们常用的new一个对象将其赋值给一个变量。那么这个变量就强引用了这个对象。

- 只有所有 GC Root 对象都不通过【强引用】引用该对象，该对象A1才能被垃圾回收

### 3.2软引用（SoftReference）

- 在垃圾回收后，内存仍不足时会再次出发垃圾回收，回收 没有其他强引用引用 仅有软引用 引用的对象。 

- 可以配合引用队列来释放软引用自身。

  > 软引用自身也是一个对象，如果在它创建时给他分配了一个引用队列（也可以不配合引用队列使用），那么在它所引用的对象A2被回收时，软引用对象就会进入这个队列。弱引用也是类似。
  >
  > 因为，不论是软引用还是弱引用，他们自身也要占用一定的内存。因为他们可能还被强引用引用着，如果想对他们占用的内存做进一步的释放，需要使用引用队列去找到他们。
  >
  > <img src="https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504301443841.png" alt="image-20250430144313777" style="zoom:50%;" />

### 3.3弱引用（WeakReference）

- 在垃圾回收时，无论内存是否充足，都会回收 没有其他强引用引用 弱引用 引用的对象 。
- 可以配合引用队列来释放弱引用自身。

### 3.4虚引用（PhantomReference）

- 虚引用必须配合引用队列使用，即在虚引用对象创建时会关联一个引用队列。虚引用中唯一的作用就是用队列接收对象即将死亡的通知。
- 主要配合 ByteBuffer使用，当创建ByteBuffer的实现类对象时，就会创建一个名为Cleaner的虚引用对象，ByteBuffer会分配一块直接内存，并且把直接内存的地址传给虚引用对象。
- 当ByteBuffer不被强引用引用时，它可以被回收，但它分配的那个直接内存并不能被java的垃圾回收管理。所以，当ByteBuffer被回收时，让虚引用对象进入引用队列，这个引用队列由 Reference Handler 线程定时的查看有没有新入队Cleaner，如果有，就调用Cleaner的clean方法，clean方法就根据记录的直接内存的地址，调用Unsafe类中的freeMemory方法释放直接内容。保证了直接内存可以被释放，不会造成内存泄漏。

### 3.5终结器引用（FinalReference）

所谓“终结器引用”，是指当某个对象即将被 GC 回收时，JVM 会检查该对象是否覆盖了 `finalize()` 方法。如果有，就会将这个对象加入一个**Finalizer Reference Queue**，交给专门的线程去执行 `finalize()` 方法

- 终结器引用用必须配合引用队列使用，即在终结器引用对象创建时会关联一个引用队列。
- 所有的java对象都会继承Object类，而Object类中有一个finalize()方法，即终结方法。当这个java对象A4重写了finalize方法，要使用这个方法释放资源时，那么，当没有强引用对象引用他时，会由JVM给他创建一个终结器引用。当A4作为垃圾被回收时，会把终结器引用加入到引用队列中，此时，A4对象还没有被垃圾回收。再由一个优先级很低的线程： Finalizer 线程，在某些时候查看这个队列中是否有终结器引用。若有，那就根据终结器引用找到被引用对象A4并调用它的 finalize  方法，下次垃圾回收时就会回收A4，这个终结器引用也被回收了。
- finalize 方法工作效率很低。因为，第一次回收A4时不能真正被回收掉，而是先要把他的终结器引用入队。并且Finalizer线程的优先级很低，这个finalize 方法被执行的机会很少，所以可能造成A4的finalize 方法迟迟不被调用，这个对象占用的内存也迟迟得不到释放。所以 不推荐使用finalize释放资源。

# 回收算法

## 1.标记清除

<img src="https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504301636253.png" alt="image-20250430163645995" style="zoom: 33%;" />

**两阶段：**

- 标记：通过可达性分析，标记出所有需要回收的对象。

- 清除：释放垃圾占用的空间。

  > 释放并不是把其中的每个字节进行清零操作，而是把内存的起始和结束的地址记录在空闲的地址列表中，下次在分配新对象的内存时，就到该列表中去找，看是否有足够的空间容纳新对象。

**弊端：**

- 造成大量的内存碎片

## 2.标记整理

<img src="https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504301643406.png" alt="image-20250430164334230" style="zoom:33%;" />

标记-整理算法的“标记”过程与“标记-清除算法”的标记过程一致，但标记之后不会直接清理。而是将所有存活对象都移动到内存的一端。移动结束后直接清理掉剩余部分。解决了空间碎片的问题。

**弊端：**

- 效率较低

## 3.复制

<img src="https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504301644061.png" alt="image-20250430164406033" style="zoom:50%;" />

将内存分成两块，每次申请内存时都使用其中的一块，当内存不够时，将这一块内存中所有存活的复制到另一块上。然后再把已使用的内存整个清理掉。然后交换两个区域的位置。解决了空间碎片的问题。

**弊端：**

- 每次在申请内存时，都只能使用一半的内存空间。内存利用率严重不足。
- 在 GC 之后存活对象较少的情况下效率比较高，但如果存活对象比较多时，会执行较多的复制操作，效率就会下降。

## 4.分代回收

![image-20250430164424442](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504301644563.png)

将堆内存划分成了新生代和老年代两个区域。新生代中又划分为伊甸园、幸存区From和幸存区To。

当创建新对象时，一般在伊甸园中。当伊甸园空间不够时，将触发一次垃圾回收，在新生代中的垃圾回收叫 Minor GC。然后根据可达性分析进行标记。然后采用复制算法将存活的对象复制到幸存区To中，同时将这些幸存的对象的寿命+1。然后交换幸存区From和幸存区To的位置。

<img src="https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202505071414564.png" alt="image-20250507141420512" style="zoom:50%;" />

当下一次Minor GC时，除了伊甸园外，要同时分析幸存区中是否有垃圾要被回收。

当幸存区中对象超过阈值（默认15次垃圾回收后还存活），就将其放入老年代中。

当老年代空间不足，会先尝试触发Minor GC，如果之后空间仍不足，那么触发Full GC，STW的时间更长。

> Minor GC会引发Stop The World。
>
> 即当发生垃圾回收时，要暂停其他的用户线程，由垃圾回收线程完成垃圾回收流程。

# 垃圾回收器

## 1.串行垃圾回收器

![image-20250507143232483](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202505071432680.png)

串行垃圾回收器分为Serial和SerialOld。

Serial工作在新生代，采用复制回收算法。

SerialOld工作在老年代，采用标记整理算法。

## 2.吞吐量优先垃圾回收器

<img src="https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202505071438796.png" alt="image-20250507143830710" style="zoom:50%;" />

ParallelGC工作在新生代，采用复制回收算法。

ParallelOldGC工作在老年代，采用标记整理算法。

![image-20250507144019119](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202505071440162.png)

## 3.响应时间优先垃圾回收器

<img src="https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202505071446326.png" alt="image-20250507144656304" style="zoom:50%;" />

CMS(Concurrent Mark Sweep)并发标记清除。工作在老年代。

ParNewGC工作在新生代，采用复制回收算法。

> 并发：当垃圾回收线程工作时，其他的用户线程可以同时进行。即它们是并发执行，他们都要去抢占CPU。
>
> 并行：当多个垃圾回收线程并行工作，不允许用户线程工作，即STW。

当用CMS发生并发失败问题时，让老年代垃圾回收器从CMS退化为SerialOld。

初始标记和重新标记阶段会触发STW。

![image-20250507145619092](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202505071456271.png)











# 类加载与字节码

```java
package cn.itcast.jvm.t5;
 // HelloWorld 示例
public class HelloWorld {
 	public static void main(String[] args) {
 		System.out.println("hello world");
 	}
}
```

编译为 HelloWorld.class 后:

```
 0000000 ca fe ba be 00 00 00 34 00 23 0a 00 06 00 15 09
 0000020 00 16 00 17 08 00 18 0a 00 19 00 1a 07 00 1b 07
 0000040 00 1c 01 00 06 3c 69 6e 69 74 3e 01 00 03 28 29
 0000060 56 01 00 04 43 6f 64 65 01 00 0f 4c 69 6e 65 4e
 0000100 75 6d 62 65 72 54 61 62 6c 65 01 00 12 4c 6f 63
 0000120 61 6c 56 61 72 69 61 62 6c 65 54 61 62 6c 65 01
 0000140 00 04 74 68 69 73 01 00 1d 4c 63 6e 2f 69 74 63
 0000160 61 73 74 2f 6a 76 6d 2f 74 35 2f 48 65 6c 6c 6f
 0000200 57 6f 72 6c 64 3b 01 00 04 6d 61 69 6e 01 00 16
 0000220 28 5b 4c 6a 61 76 61 2f 6c 61 6e 67 2f 53 74 72
 0000240 69 6e 67 3b 29 56 01 00 04 61 72 67 73 01 00 13
 0000260 5b 4c 6a 61 76 61 2f 6c 61 6e 67 2f 53 74 72 69
 0000300 6e 67 3b 01 00 10 4d 65 74 68 6f 64 50 61 72 61
 0000320 6d 65 74 65 72 73 01 00 0a 53 6f 75 72 63 65 46
 0000340 69 6c 65 01 00 0f 48 65 6c 6c 6f 57 6f 72 6c 64
 0000360 2e 6a 61 76 61 0c 00 07 00 08 07 00 1d 0c 00 1e
 0000400 00 1f 01 00 0b 68 65 6c 6c 6f 20 77 6f 72 6c 64
 0000420 07 00 20 0c 00 21 00 22 01 00 1b 63 6e 2f 69 74
 0000440 63 61 73 74 2f 6a 76 6d 2f 74 35 2f 48 65 6c 6c
 0000460 6f 57 6f 72 6c 64 01 00 10 6a 61 76 61 2f 6c 61
 0000500 6e 67 2f 4f 62 6a 65 63 74 01 00 10 6a 61 76 61
 0000520 2f 6c 61 6e 67 2f 53 79 73 74 65 6d 01 00 03 6f
 0000540 75 74 01 00 15 4c 6a 61 76 61 2f 69 6f 2f 50 72
 0000560 69 6e 74 53 74 72 65 61 6d 3b 01 00 13 6a 61 76
 0000600 61 2f 69 6f 2f 50 72 69 6e 74 53 74 72 65 61 6d
 0000620 01 00 07 70 72 69 6e 74 6c 6e 01 00 15 28 4c 6a
 0000640 61 76 61 2f 6c 61 6e 67 2f 53 74 72 69 6e 67 3b
 0000660 29 56 00 21 00 05 00 06 00 00 00 00 00 02 00 01
 0000700 00 07 00 08 00 01 00 09 00 00 00 2f 00 01 00 01
 0000720 00 00 00 05 2a b7 00 01 b1 00 00 00 02 00 0a 00
 0000740 00 00 06 00 01 00 00 00 04 00 0b 00 00 00 0c 00
 0000760 01 00 00 00 05 00 0c 00 0d 00 00 00 09 00 0e 00
 0001000 0f 00 02 00 09 00 00 00 37 00 02 00 01 00 00 00
 0001020 09 b2 00 02 12 03 b6 00 04 b1 00 00 00 02 00 0a
 0001040 00 00 00 0a 00 02 00 00 00 06 00 08 00 07 00 0b
 0001060 00 00 00 0c 00 01 00 00 00 09 00 10 00 11 00 00
 0001100 00 12 00 00 00 05 01 00 10 00 00 00 01 00 13 00
 0001120 00 00 02 00 14
```



## 1. 类文件结构

根据JVM规范，类文件结构：（各厂商在实现JVM时必须遵从该规范）

```
ClassFile {
    u4             magic; // 魔术
    u2             minor_version;// 小版本号
    u2             major_version;// 主版本号
    u2             constant_pool_count;// 常量池的数量
    cp_info        constant_pool[constant_pool_count-1];// 常量池信息
    u2             access_flags;// 访问修饰。（例如：该类是公共的还是私有的）
    u2             this_class;//当前类信息
    u2             super_class;//父类名信息
    u2             interfaces_count;//接口数量
    u2             interfaces[interfaces_count];//一个类可以实现多个接口
    u2             fields_count;//类中字段数量
    field_info     fields[fields_count];//一个类可以有多个字段
    u2             methods_count;//类中方法数量
    method_info    methods[methods_count];//一个类可以有个多个方法
    u2             attributes_count;//此类的属性表中的属性数。（类的附加属性信息）
    attribute_info attributes[attributes_count];//属性表集合
}
```

u4代表4个字节。

### 1.1 魔数

所有文件都有一个自己的特定类型，例如：java文件用4个字节表示这是一个java class文件，而不是其他的文件。

不同的文件有不同的魔数信息。例如，一个png或者jdp图片，它们的魔数信息是不同的。即魔数：标识该文件是什么类型。

java中，0~3字节，表示它是否是class类型文件，用 ca fe ba be表示。咖啡baby。

### 1.2 版本

4~7字节，表示类的版本。例如：00 00 00 34。这里小版本没有表示出来。主版本为00 34（16进制->10进制为 52）表示 jdk 8。 51 表示 jdk7。53 表示 jdk9。

### 1.3 常量池

8~9字节，表示常量池长度，常量池中项的数目。例如：00 23（16进制->10进制为 35）表示常量池中一共有35项（#1~#34项），注意：#0项不计入，也没有值。即，最终有34项。

![image-20250324115418546](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202503241154655.png)

第#1项 0a表示该常量项是什么类型。需要查表。例如：0a（10）-> CONSTANT_methodref 方法引用信息。方法信息的长度是固定的，接下来的4个字节就代表方法相关信息。例如：前两个字节为 00 06 代表 它引用了常量池中第#6项，代表方法的所属类。通过一层层查找，知道了 它是Object类的方法。具体是哪个方法，查看后两个字节为 00 15（21）代表它引用了常量池中第#21项，代表方法名信息。综上，第#1项 是Object类下的无参构造方法。即，通过 0a 00 06 00 15 我们可以知道：在常量池中有一项定义了方法，方法所属类和方法名通过查常量池中另外两项也可以知道。

第#2项 09 -> CONSTANT_Fieldref，表示是Field信息。后4个字节00 16（22） 00 17 （23）表示引用了常量池中#22和#23项获得该成员变量的所属类和成员变量名。综上，第#2项是System类下的一个成员变量，成员变量的名字是out，成员变量类型是PrintStream。

第#3项08 -> CONSTANT_String，表示一个字符串常量名称，后两个字节 00 18（24）表示引用了常量池中#24项。综上，第#3项是字符串常量“hello world”。

第#4项0a -> CONSTANT_methodref表示一个方法引用信息。后4个字节 00 19（25） 00 1a（26）表示引用了常量池中#25和#26项。综上，第#4项是 PrintStream类下的println方法，方法参数类型是String类，返回值类型是void 。

第#5项 07 -> CONSTANT_Class表示是Class信息，后两个字节 00 1b（27）代表 去查常量池中第#27项。是本类的信息。

第#6项 07 -> CONSTANT_Class表示是Class信息，后两个字节 00 1c（28）代表 去查常量池中第#28项，就知道Class信息了。是父类的信息。

第#7项 01 -> CONSTANT_Utf8表示是一个Utf8串，后两个字节00 06 表示该Utf8串的长度 6个字节。那么，查看接下来的6个字节 3c 69 6e 69 74 3e，转换成字符是 <init>。表示是一个init方法，即构造方法。 

第#8项 01 -> CONSTANT_Utf8表示是一个Utf8串，后两个字节00 03 表示该Utf8串的长度 3个字节。那么，查看接下来的3个字节 28 29 56 转换成字符是 ()V 。表示无参、无返回值。

第#9项 01 -> CONSTANT_Utf8表示是一个Utf8串，后两个字节00 04 表示该Utf8串的长度 4个字节。那么，查看接下来的4个字节，转换成字符是 Code。 代表方法的一个属性。字节码。

第#10项 01 -> CONSTANT_Utf8表示是一个Utf8串，后两个字节00 0f 表示该Utf8串的长度 15个字节。那么，查看接下来的15个字节，转换成字符是 LineNumberTable。 代表方法的一个属性。方法的行号表。

第#11项 01 -> CONSTANT_Utf8表示是一个Utf8串，后两个字节00 12 表示该Utf8串的长度 18个字节。那么，查看接下来的18个字节，转换成字符是 LocalVariableTable。 代表方法的一个属性。方法的本地变量表。

第#12项 01 -> CONSTANT_Utf8表示是一个Utf8串，后两个字节00 04 表示该Utf8串的长度 4个字节。那么，查看接下来的4个字节，转换成字符是 this。 

第#13项 01 -> CONSTANT_Utf8表示是一个Utf8串，后两个字节00 1d 表示该Utf8串的长度29个字节。那么，查看接下来的29个字节，转换成字符是 Lcn/itcast/jvm/t5/HeloWorld;。在字节码中，L开头，分号结尾，表示一个具体的类型。中间是类型的全路径。

第#14项 01 -> CONSTANT_Utf8表示是一个Utf8串，后两个字节00 04 表示该Utf8串的长度 4个字节。那么，查看接下来的4个字节，转换成字符是 main。

第#15项 01 -> CONSTANT_Utf8表示是一个Utf8串，后两个字节00 16 表示该Utf8串的长度 22个字节。那么，查看接下来的22个字节，转换成字符是 ([Ljava/lang/String;)V。表示字符串类型的数组。[表示是一个数组。返回值类型是void。

第#16项 01 -> CONSTANT_Utf8表示是一个Utf8串，后两个字节00 04 表示该Utf8串的长度 4个字节。那么，查看接下来的4个字节，转换成字符是 args。

第#17项 01 -> CONSTANT_Utf8表示是一个Utf8串，后两个字节00 13 表示该Utf8串的长度 19个字节。那么，查看接下来的19个字节，转换成字符是 [Ljava/lang/String;。进一步描述了一下args参数的类型，是一个字符串数组。

第#18项 01 -> CONSTANT_Utf8表示是一个Utf8串，后两个字节00 10 表示该Utf8串的长度 16个字节。那么，查看接下来的16个字节，转换成字符是 MethodParameters。代表方法的一个属性。方法的参数名称信息。

第#19项 01 -> CONSTANT_Utf8表示是一个Utf8串，后两个字节00 0a 表示该Utf8串的长度 10个字节。那么，查看接下来的10个字节，转换成字符是 SourceFile。代表类的一个属性。类的源文件。

第#20项 01 -> CONSTANT_Utf8表示是一个Utf8串，后两个字节00 0f 表示该Utf8串的长度 15个字节。那么，查看接下来的15个字节，转换成字符是 HelloWorld.java。即第#19 #20项结合起来就是，这个类的源文件是HelloWorld.java。

第#21项 0c ->  CONSTANT_NameAndType表示 名称+类型。后4个字节 00 07 00 08 代表引用了常量池中#7 #8两项，用来确定方法的名称和方法的参数类型、返回值类型。

第#22项 07 ->  CONSTANT_Class表示是Class信息。后两个字节 00 1d（29）引用了常量池中第#29项。

第#23项 0c ->  CONSTANT_NameAndType表示 名称+类型。后4个字节 00 1e（30） 00 1f（31） 代表引用了常量池中#30 #31两项，用来确定成员变量的名称和成员变量的类型。

第#24项 01-> CONSTANT_Utf8表示是一个Utf8串。后两个字节 00 0f（15） 代表 该Utf8串的长度 15个字节。那么，查看接下来的15个字节 00 0b 68 65 6c 6c 6f 20 77 6f 72 6c 64 ，转换成字符是 hello world。

第#25项 07 ->  CONSTANT_Class表示是Class信息。后两个字节 00 20（32）引用了常量池中第#32项。

第#26项 0c ->  CONSTANT_NameAndType表示 名称+类型。后4个字节 00 21（33） 00 22（34） 代表引用了常量池中#33 #34两项。用来确定方法的名称和方法的参数类型、返回值类型。

第#27项 01-> CONSTANT_Utf8表示是一个Utf8串。后两个字节 00 1b（27）代表 该Utf8串的长度 27个字节。那么，查看接下来的27个字节，转换成字符是 cn/itcast/jvm/t5/HelloWorld。即该类文件的包名+类名，即全限定名。

第#28项 01-> CONSTANT_Utf8表示是一个Utf8串。后两个字节 00 10（16）代表 该Utf8串的长度 16个字节。那么，查看接下来的16个字节 6a 61 76 61 2f 6c 61 6e 67 2f 4f 62 6a 65 63 74，转换成字符是 java/lang/Object。表示这个是一个Object类。

第#29项 01-> CONSTANT_Utf8表示是一个Utf8串。后两个字节 00 10（16）代表 该Utf8串的长度 16个字节。那么，查看接下来的16个字节 6a 61 76 61 2f 6c 61 6e 67 2f 53 79 73 74 65 6d，转换成字符是java/lang/System。表示这个是一个System类。

第#30项 01-> CONSTANT_Utf8表示是一个Utf8串。后两个字节 00 03 代表 该Utf8串的长度 3个字节。那么，查看接下来的3个字节 6f 75 74 ，转换成字符是 out 。

第#31项 01-> CONSTANT_Utf8表示是一个Utf8串。后两个字节 00 15（21）代表 该Utf8串的长度 21个字节。那么，查看接下来的21个字节 4c 6a 61 76 61 2f 69 6f 2f 50 72 69 6e 74 53 74 72 65 61 6d 3b 转换成字符是java/io/PrintStream。表示这个是一个PrintStream类。

第#32项 01-> CONSTANT_Utf8表示是一个Utf8串。后两个字节 00 13（19）代表 该Utf8串的长度 19个字节。那么，查看接下来的19个字节，转换成字符是java/io/PrintStream。表示这个是一个PrintStream类。

第#33项 01-> CONSTANT_Utf8表示是一个Utf8串。后两个字节 00 07 代表 该Utf8串的长度 7 个字节。那么，查看接下来的7个字节，转换成字符是 println。

第#34项 01-> CONSTANT_Utf8表示是一个Utf8串。后两个字节 00 15（21）代表 该Utf8串的长度 21个字节。那么，查看接下来的21个字节，转换成字符是 (java/lang/String;)V。表示println方法的参数类型是String类，返回值类型是void。

### 1.4 访问标识与继承信息

![image-20250324155934051](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202503241605060.png)

ACC_SUPER：表示一个类。

ACC_SYNTHETIC：表示是人工合成的，不是源代码。

ACC_ANNOTATION：表示是一个注解。

在常量池看完之后，接着往后看。

比如，后面两个字节是 00 21，那么查上面的表，看哪几项加起来是 00 21。可以看到，00 20 + 00 01 = 00 21。表示它是一个公共的类。

再往后两个字节是 00 05 表示本类的全限定名，从常量池中第#5项中找。

再往后两个字节是 00 06 表示父类的全限定名，从常量池中第#6项中找。

再往后两个字节是 00 00 表示接口的数量。这里为0，如果有的话 后面还会有一些接口的信息。

# 内存模型（Java Memory  Model-JMM）

JMM 定义了一套在**多线程**读写共享数据时（成员变量、数组）时，对数据的可见性、有序 性、和原子性的规则和保障

## 1.原子性

### 1.1问题

两个线程对初始值为 0 的静态变量一个做自增，一个做自减，各做 5000 次，结果不一定是0.

因为 Java 中对静态变量的自增，自减并不是原子操作。

例如，如对于 i++ 而言（i 为静态变量），实际会产生如下的 JVM 字节码指令：

```
getstatic   i  // 获取静态变量i的值
iconst_1       // 准备常量1
iadd           // 加法
putstatic   i  // 将修改后的值存入静态变量i
```

java的内存模型：分为主内存和工作内存。

静态变量的自增和自减需要在主存和线程内存中进行数据交换。

![image-20250401155757723](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504011558980.png)

如果是单线程：顺序执行，不会交错执行。

```
// 假设i的初始值为0
getstatic     i  // 线程1：获取静态变量i的值 线程内i=0
iconst_1         // 线程1：准备常量1
iadd             // 线程1：自增，线程内i=1
putstatic     i  // 线程1：将修改后的值存入静态变量i 静态变量i=1

getstatic     i  // 线程1：获取静态变量i的值 线程内i=1
iconst_1         // 线程1：准备常量1
isub             // 线程1：自减，线程内i=0
putstatic     i  // 线程1：将修改后的值存入静态变量i 静态变量i=0
```

如果是多线程：由于cpu的时间片抢占式算法，因此会交错执行。

出现负数的情况：

```
// 假设i的初始值为0
getstatic     i  // 线程1：获取静态变量i的值 线程内i=0
getstatic     i  // 线程2：获取静态变量i的值 线程内i=0
iconst_1         // 线程1：准备常量1
iadd  			 // 线程1：自增，线程内i=1
putstatic     i  // 线程1：将修改后的值存入静态变量i 静态变量i=1
iconst_1         // 线程2：准备常量1
isub             // 线程2：自减，线程内i=-1
putstatic     i  // 线程2：将修改后的值存入静态变量i 静态变量i=-1
```

出现正数的情况：

```
// 假设i的初始值为0
getstatic     i  // 线程1：获取静态变量i的值 线程内i=0
getstatic     i  // 线程2：获取静态变量i的值 线程内i=0
iconst_1         // 线程1：准备常量1
iadd  			 // 线程1：自增，线程内i=1
iconst_1         // 线程2：准备常量1
isub             // 线程2：自减，线程内i=-1
putstatic     i  // 线程2：将修改后的值存入静态变量i 静态变量i=-1
putstatic     i  // 线程1：将修改后的值存入静态变量i 静态变量i=1
```

### 1.2解决方法

通过**synchonized**（同步关键字）保证原子性

```java
synchronized( 对象 ) { // 两个线程锁住的必须是同一个对象
	要作为原子操作代码
}
```

## 2.可见性

### 2.1问题

main 线程对 run 变量的修改对于 t 线程不可见，导致了 t 线程无法停止：

```java
static boolean run = true;

public static void main(String[] args) throws InterruptedException {
    Thread t = new Thread(() -> {
        while (run) {
            // .... 这里的循环会一直运行
        }
    });

    t.start();  // ① 启动子线程 t

    Thread.sleep(1000); // ② 主线程睡眠 1 秒

    run = false; // ③ 主线程修改 run 变量
}
```

分析：

1. 初始状态， t 线程刚开始从主内存读取了 run 的值到工作内存。

![image-20250401162421908](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504011624984.png)

2.因为 t 线程要频繁从主内存中读取 run 的值（每次while都会去读），JIT 编译器会将 run 的值缓存至自己工作内存中的高 速缓存中，减少对主存中 run 的访问，提高效率

![image-20250401162444465](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504011624538.png)

3.1 秒之后，main 线程修改了 run 的值，并同步至主存，而 t 是从自己工作内存中的高速缓存中读 取这个变量的值，结果永远是旧值

![![](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504011625401.png)](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504011625401.png)

### 2.2解决方法

**volatile**（易变关键字）

可以用来修饰成员变量和静态成员变量，他可以避免线程从自己的工作缓存中查找变量的值，必须到主内存中获取它的值，线程操作 volatile 变量都是直接操作主内存中的对应变量。

### 2.3可见性

上述例子体现的就是可见性，它保证的是在多个线程之间，一个线程对 volatile 变量的修改对另一 个线程可见， 但不能保证原子性，仅适用于在一个写线程，多个读线程的情况。

即保证一个线程对 volatile 变量进行写操作后，其他线程读该 volatile 变量时可以读取到最新值。但是，在出现原子性问题时，是线程1已经读取到了变量，然后另线程2抢占了cpu执行权，那么当执行权回到线程1时，它不会再去主内存中读该变量，而是直接用之前已经读到的进行操作，因为volatile失效。

> synchronized 语句块既可以保证代码块的原子性，也同时保证代码块内变量的可见性。但缺点是 synchronized是属于重量级操作，性能相对更低。而volatile只能保证可见性。属于轻量级操作。

> synchronized可以保证原子性是因为：
>
> JMM规定了`synchronized` 的两大特性：
>
> 1.进入 `synchronized` 代码块时，必须从主内存读取共享变量的最新值（保证可见性）。
>
> 2.退出 `synchronized` 代码块时，必须将变量的修改刷新回主内存（保证修改对其他线程可见）。

### 2.4扩展

当在上述while循环中加入了：

```java
 System.out.println() // 例如: System.out.println(1) 
```

那么，此时不加volatile关键字也可以。

这是因为：

![image-20250401163905618](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504011639741.png)

println方法内部使用了synchronized关键字，要对当前this，即PrintStream进行同步。那么就会强制当前线程读取主内存中的值，破坏了JIT的优化。

## 3.有序性

### 3.1问题

```java
    int num = 0;
    boolean ready = false;

    // 线程1 执行此方法
    public void actor1(I_Result r) {
        if (ready) {
            r.r1 = num + num;
        } else {
            r.r1 = 1;
        }
    }

    // 线程2 执行此方法
    public void actor2(I_Result r) {
        num = 2;
        ready = true;
    }
```

其中， I_Result 是一个对象，有一个属性 r1 用来保存结果。结果有几种？

分析：

1.线程1 先执行，这时 ready = false，所以进入 else 分支结果为 1

2.线程2 先执行 num = 2，但没来得及执行 ready = true，线程1 执行，还是进入 else 分支，结果为1

3.线程2 执行到 ready = true，线程1 执行，这回进入 if 分支，结果为 4（执行了num）

**4.**线程2 执行 ready = true，切换到线程1，进入 if 分支，相加为 0，再切回线程2 执行  num = 2。

上述第4种现象叫做**指令重排**，是 JIT 编译器在运行时的一些优化。

### 3.2解决方法

加上volatile关键字可以禁用指令重排。

```java
volatile boolean ready = false;
```

### 3.3有序性理解

JVM 会在不影响正确性的前提下，调整语句的执行顺序

```java
static int i;
static int j;
 // 在某个线程内执行如下赋值操作
i = ...; // 较为耗时的操作
j = ...; 
```

可以看到，先执行 i  还是 先执行  j ，对最终的结果不会产生影响。所以，上面代码真正执行时，既可以是

```java
i = ...; // 较为耗时的操作
j = ...;
```

也可以是

```java
j = ...;
i = ...; // 较为耗时的操作
```

这种特性称之为『指令重排』。在单线程情况下没有问题，但在多线程情况下就会出现问题。

例如：著名的 double-checked  locking（双重判定锁） 模式实现单例

```java
public final class Singleton {
    private static Singleton INSTANCE = null;

    private Singleton() { }

    public static Singleton getInstance() {
        // 实例没创建，才会进入内部的 synchronized 代码块
        if (INSTANCE == null) {            
            synchronized (Singleton.class) {
                // 也许有其它线程已经创建实例，所以再判断一次
                if (INSTANCE == null) {
                    INSTANCE = new Singleton();
                }
            }
        }
        return INSTANCE;
    }
}
```

以上的实现特点是： 

1.懒惰实例化 

2.首次使用创建实例时调用getInstance()才使用synchronized加锁，后续再创建实例时无需加锁（如果直接给getInstance方法上加锁，粒度就太大了，每次调用方法都会加锁）

但在多线程环境下，上面的代码是有问题的，他没有考虑指令重排的问题。

`INSTANCE = new Singleton() `对应的字节码为：

```
0:   new           #2      // class cn/itcast/jvm/t4/Singleton （创建 Singleton 对象：先给对象分配空间，执行结果																	会把对象的引用放入操作数栈）
3:   dup                   // （操作数栈把这个引用复制了一份，此时，栈顶有两个对象的引用）
4:   invokespecial #3      // Method "<init>":()V （第一个对象引用地址交给invokespecial调用构造方法<init>）
7:   putstatic     #4      // Field （第二个对象引用地址交给putstatic给INSTANCE静态变量赋值）
```

其中 4 7 两步的顺序不是固定的，也许 jvm 会指令重排优化为：先将引用地址赋值给 INSTANCE 变量后，再执行 构造方法，如果两个线程 t1，t2 按如下时间序列执行：

```
时间1  t1 线程执行到 INSTANCE = new Singleton(); 
时间2  t1 线程分配空间，为Singleton对象生成了引用地址（0 处）
时间3  t1 线程将引用地址赋值给 INSTANCE，这时 INSTANCE != null（7 处）
时间4  t2 线程进入getInstance() 方法，发现 INSTANCE != null（synchronized块外），直接返回 INSTANCE
时间5  t1 线程执行Singleton的构造方法（4 处）
```

这时 t1 还未完全将构造方法执行完毕，如果在构造方法中要执行很多初始化操作，那么 t2 拿到的是将 是一个未初始化完毕的单例

对 INSTANCE 使用 volatile 修饰即可，可以禁用指令重排，（在 JDK 5 以上的版本的 volatile 才 会真正有效）

### 3.4happens-before

happens-before 规定了哪些写操作对其它线程的读操作可见，它是可见性与有序性的一套规则总结， 抛开以下 happens-before 规则，JMM 并不能保证一个线程对共享变量的写，对于其它线程对该共享变 量的读可见

## 4.CAS与原子类

CAS 即  Compare and Swap，它体现的一种乐观锁的思想。配合volatile使用，不同于synchronized，它不会加锁。

比如多个线程要对一个共享的整型变量执行 +1 操作：

```java
// 需要不断尝试
while (true) { 
    int 旧值 = 共享变量;  // 比如拿到了当前值 0。 （将共享变量从主内存中读到工作内存中）
    int 结果 = 旧值 + 1;   // 在旧值 0 的基础上增加 1，正确结果是 1 

    /*
     * compareAndSwap(旧值, 结果) 会尝试把结果赋值给共享变量，赋值的同时会比较旧值和当前的旧值是否相同
     * 这时候如果别的线程把共享变量改成了 5，本线程的正确结果 1 就作废了，
     * 这时候 compareAndSwap 返回 false，重新尝试，直到：
     * compareAndSwap 返回 true，表示我本线程做修改的同时，别的线程没有干扰
     */
    if (compareAndSwap(旧值, 结果)) { 
        // 成功，退出循环
        break;
    }
}
```

获取共享变量时，为了保证该变量的可见性，需要使用 volatile 修饰。结合 CAS 和 volatile 可以实现无 锁并发，适用于竞争不激烈、多核 CPU 的场景下。

- 因为没有使用 synchronized，所以线程不会陷入阻塞，这是效率提升的因素之一 

- 但如果竞争激烈，可以想到重试必然频繁发生，反而效率会受影响
