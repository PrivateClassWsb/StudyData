在需要运行 Java 应用程序的操作系统上，安装一个与操作系统对应的Java虚拟机(JVM Java Virtual Machine)即可。

JVM 虚拟机本身不允许跨平台，允许跨平台的是 Java 程序



字节码文件名称取决于对应java文件的类名称。

比如：java文件名为：A.java 其中的类名称为class ABC。那么编译A.Java后将得到ABC.class。

上述情况java文件名和类名称可以不一样。当把Class ABC改为public class ABC后，java文件名和类名称就必须一致了。此时再编译A.java就会报错，无法生成字节码文件。



![image-20241015164333412](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/TyporaPic/image-20241015164333412.png)

# **JVM、JRE和JDK的关系：**

![image-20241015164615027](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/TyporaPic/image-20241015164615027.png)

核心类库：比如System、String

# 注释

- 单行注释

```java
// 注释信息
```

- 多行注释

```java
/* 
	注释信息1
	注释信息2
*/
```

- 文档注释

```java
/** 
	注释信息1
	注释信息2
*/

```

- 注释内容不会参与编译和运行

# 变量

变量就是内存中的存储空间

- 变量名不允许重复定义


- 一条语句可以定义多个变量


- 变量在使用之前一定要进行赋值

```
定义：数据类型 变量名 = 数据值；
```

# 标识符

标识符：就是给类，方法，变量等起的名字。

- **标识符命名规则**：

1.由数字、字母、下划线(_)和美元符($)组成

2.不能以数字开头

3.不能是关键字

4.区分大小写

- **标识符命名规范**：

1.小驼峰命名法:name/firstName. 一般是变量名

2.大驼峰命名法:Student/GoodStudent. 一般是类名

# 数据类型

**基本数据类型**：

![image-20241015171521380](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/TyporaPic/image-20241015171521380.png)

定义long类型时：

```java
long a = 41654654 //报错
//正确写法：
long b = 41654654l
long a = 41654654L
```

定义float类型时：

```java
float a = 183.1 //报错
//正确写法：
float b = 183.1f
float a = 183.1F
```

# Scanner键盘录入

```java
import java.util.Scanner;
Scanner sc = new Scanner(System.in);
int age = sc.nextInt();
String name = sc.next();
```

# 运算符

## 1.算术运算符

+-*/%

整数操作只能得到整数，要想得到小数，必须有浮点数参与运算。

**字符串拼接操作**：

当  +  操作中，遇到了字符串，这时 + 就是**字符串连接符**，而不是算术运算。

```java
System.out.println(1 + 23); // 24
System.out.println("年龄为:" + 23); // 年龄为23
System.out.println(1 + 99 + "年黑马"); // 100年黑马
System.out.println("年龄为:" + 23 + 1); // 年龄为231
System.out.println("年龄为:" + (23 + 1) ); // 年龄为24
```

## 2.自增自减运算符

++ --

- 单独使用

  ++ 和 -- 无论是放在变量的前边还是后边，结果是一样的。

- 参与操作使用

  如果放在变量的后面，先拿变量的值进行运算，再对变量的值进行+1、-1 

  ```java
  int a = 10;
  int b = a++; // b = 10
  ```

  如果放在变量的前边，先对变量进行+1、-1，再拿变量的值进行运算

  ```java
  int a = 10;
  int b = ++a; // b = 11
  ```

l++ 、-- 只能操作变量，不能操作常量

## 3.类型转换

boolean类型不能转换为其他的数据类型。

### 3.1隐式转换

取值范围小的数值->取值范围大的数值

取值范围：byte<short=char<int<long<float<double

当数据类型不一样的时候需要转换成一样的才可以计算。

转换规则：取值范围小的和取值范围大的计算时候，小的会先提升为大的再进行计算；byte short char三种类型的数据在运算的时候都会先提升为int再进行计算。

### 3.2强制转换

取值范围大的数值->取值范围小的数值

强转中的精度损失：

```java
int a = 130;
byte b = (byte)a; //-126
// 130的二进制形式：00000000 00000000 00000000 10000010 -> 转换为补码形式：00000000 00000000 00000000 10000010
// 4个字节，强制转换为1个字节，就是砍掉前3组8位 -> 10000010 (此时还是补码的状态) -> 转换为原码：11111110（-126）
```

### 3.3面试题

```java
public class TypeConversionTest {
    /*
        类型转换面试题 : 下列代码是否存在错误, 如果有, 请指出说明, 并改正.

        byte b1 = 3;
        byte b2 = 4;
        byte b3 = b1 + b2;

        错误原因:
                    b1和b2是两个byte类型, 在运算的时候, 会提升为int类型
                    提升之后, 就是两个int在运算了, 运算的结果还是int.
                    将int类型的结果, 赋值给byte类型的变量, 属于大给小, 不能直接给.

                    byte b1 = 3;
                    byte b2 = 4;
                    byte b3 = (byte)(b1 + b2); 或 int b3 = b1 + b2;

                    System.out.println(b3);

        byte b = 3 + 4; 这句代码是否可以通过编译？

        （这里的3和4是两个字面量, 所有整数默认都是int类型, 所以这里应该是两个int做运算
        运算后的结果还是int, int类型结果给byte类型变量赋值, 大的给小的, 不能直接给, 编译错误.）
        上述这个说法是错误的。

        因为Java存在常量优化机制 : 在编译的时候 (javac) 就会将3和4这两个字面量进行运算.
                                产生的字节码文件 :  byte b = 7;
     */
    public static void main(String[] args) {
        byte b = 3 + 4;
        System.out.println(b); // 7
    }
}
```

## 4.赋值运算符

= += -= *= /= %=

扩展的赋值运算符内部自带强转效果。

```java
short s = 1;
s = s + 1; //错误。

short s = 1;
s += 1; //正确。 真实样子：s = (short)(s + 1)

double a = 12.3
int b = 10;
b += a; // 22    b = (int)(b + a)
```

## 5.关系运算符

== != > >= < <=

## 6.逻辑运算符

| 符号 | 介绍     | 说明                      |
| ---- | -------- | ------------------------- |
| &    | 逻辑与   | 并且, 遇 false 则 false   |
| \|   | 逻辑或   | 或者，遇 true 则 true     |
| ！   | 逻辑非   | 取反                      |
| ^    | 逻辑异或 | 相同为 false，不同为 true |

**短路逻辑运算符**：

| 符号 | 介绍   | 说明                           |
| ---- | ------ | ------------------------------ |
| &&   | 短路与 | 作用和 & 相同，但是有短路效果  |
| \|\| | 短路或 | 作用和 \| 相同，但是有短路效果 |

- 逻辑与 &，无论左边 true false，右边都要执行。

  短路与 &&，如果左边为 true，右边执行；如果左边为 false，右边不执行。

- 逻辑或 **|**，无论左边 true false，右边都要执行。

  短路或 **||**，如果左边为 false，右边执行；如果左边为 true，右边不执行。

## 7.三元运算符

格式：判断条件 **?** 值1 **:** 值2;

## 8.算术移位和逻辑移位

**逻辑移位**：>>>

逻辑右移：无论正负数，左边都补0，右边丢失。

**算数移位**：>>   <<

算术左移：右边空位补0。左边丢失。所以可能负数变为正数。

算术右移：左边用符号位补充。右边丢失。

## 9.运算符优先级

![image-20241015200231123](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/TyporaPic/image-20241015200231123.png)	

# 原码反码补码

## 原码

高位：二进制数据中，最左侧的数据。通过高位代表符号位：0 代表正数，1 代表负数。其余位，表示数值大小

二进制中如果只有原码状态就会出现问题：遇到负数运算，会出现错误。（因为正数的原码、反码和补码都是一样的，所以不会出现错误）

比如：

![image-20241015192903469](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/TyporaPic/image-20241015192903469.png)

因此，计算机在运算的时候，都是以二进制**补码**的形式在运算

## 反码

正数的反码与其原码相同

负数的反码是对其原码逐位取反，但符号位除外。

## 补码

正数的补码与其原码相同

负数的补码是在其反码的末位加1

因此上述例子正确计算为：

![image-20241015193345192](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/TyporaPic/image-20241015193345192.png)



# 方法重载

同一个类中，方法名相同，参数不同的方法

参数不同：个数不同、类型不同、顺序不同

**注意：识别方法之间是否是重载关系，只看方法名和参数，跟返回值无关。**

# **数组**

数组是一种容器，可以用来存储同种数据类型的多个值。

在存储数据的时候需要结合隐式转换来考虑。例如：有一个int类型的数组容器，boolean类型和double类型的值都不能存放，而byte、short、int的值都可以存放。例如：有一个double类型的数组容器，byte、short、int、long、float类型的数据都可以存放。

## 1.一维数组

```java
// 静态初始化
int[] array =  new int[]{11,22,33}
int[] array =  {11,22,33}
int array[]  =  {11,22,33}
// 动态初始化：初始化时只指定数组的长度，由系统为数组分配初始值。
int[] arr = new arr[3]; 
```

**数组的地址值：**表示数组在内存中的位置。

```java
int[] arr = {1,2,3,4}
System.out.println(arr); //[I@3b07d329
```

**地址值的格式含义：**

“ [ ”：表示当前是一个数组

“ I ”：表示当前数组里的元素都是int类型

“ @ ”：表示一个分隔符（固定格式）

“ 3b07d329 ”：数组真正的地址值（十六进制）

平时习惯把"[I@3b07d329"这个整体叫作数组的地址值。

**数组默认初始化的规律:**

整数类型：0

小数类型：0.0

字符类型："\u0000" 即空格

布尔类型：false

引用数据类型：null

## 2.二维数组

```java
// 静态初始化
int[][] array =  new int[][]{{11,22},{33,44}}
int[][] array =  {{11,22},{33,44}}
int array[][] =  {{11,22},{33,44}} // 这种也可以，但根据阿里巴巴编码规范，一般用上面的。
// 动态初始化
int[][] array =  new int[2][3]
int[][] array = new int[2][];
int[][] array = new int[][3]; // 错误
```

# 构造方法

* 构造方法的创建

如果没有定义构造方法，系统将给出一个默认的无参数构造方法

如果定义了构造方法，系统将不再提供默认的构造方法

* 构造方法的重载

如果自定义了带参构造方法，还要使用无参数构造方法，就必须再写一个无参数构造方法

# this

- 当局部变量和成员变量重名的时候, Java 使用的是**就近原则**

- this.本类成员变量 : 如果不涉及变量重复, this.可以省略

- this代表当前类对象的引用（地址）


![image-20241021183915714](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/TyporaPic/image-20241021183915714.png)

# String类

## **特点**

1.Java 程序中所有双引号字符串, 都是 String 类的对象 

2.字符串在创建之后, 其内容不可更改

3.字符串虽然不可改变, 但是可以被共享

## **常见构造方法**

> | 构造方法                       | 说明                             |
> | ------------------------------ | -------------------------------- |
> | public String()                | 创建空白字符串，不含任何内容     |
> | public String(String original) | 根据传入的字符串，创建字符串对象 |
> | public String(char[] chs)      | 根据字符数组，创建字符串对象     |

## **面试题**

```java
public class StringTest1 {    
    public static void main(String[] args) {        
        String s1 = "abc";        
        String s2 = "abc";        
        System.out.println(s1 == s2); //true
    }
}

```

![image-20241021184952241](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/TyporaPic/image-20241021184952241.png)

```java
public class StringTest2 {    
    public static void main(String[] args) {        
        String s1 = "abc";       // 由双引号最直接创建出来的对象，会在StringTable中进行维护  
        String s2 = new String("abc");  // 通过构造方法new出来的对象，会在堆内存中有自己独立的内存空间。
        System.out.println(s1 == s2); //false
    }
}
```

![image-20241021191059155](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/TyporaPic/image-20241021191059155.png)

**注1：**s2会检查StringTable中是否有“abc”，若有，则会将StringTable中的“abc”拷贝一份副本到新创建的这块空间中。

Api帮助文档：

![image-20241021185923406](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/TyporaPic/image-20241021185923406.png)

**注2：**拷贝过来的不是内容，而是地址。

源码：

![image-20241021190151637](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/TyporaPic/image-20241021190151637.png)

this.value中value是指： ![image-20241021190227012](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/TyporaPic/image-20241021190227012.png)

所以，字符串在底层就是这样一个字节数组。上述new就是在给这样一个字节数组赋值。

```java
this.value = original.value; 
//拿着传过来的字符串对象,original.value调用了内部的那个字节数组，把这个字节数组的地址值交给了this.value现有的这个字符串对象内部的数组
//所以，上述是地址之间的赋值
//即this.value是一个数组，original.value也是一个数组。数组给数组赋值，就是地址的赋值
```

![image-20241021191318669](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/TyporaPic/image-20241021191318669.png)

```java
public class StringTest3 {
    public static void main(String[] args) {
        String s1 = "abc";
        String s2 = "ab";
        String s3 = s2 + "c";
        System.out.println(s1 == s3); //false
    }
}
```

![image-20241021191643060](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/TyporaPic/image-20241021191643060.png)

api帮助文档：

<img src="https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/TyporaPic/image-20241021192012301.png" alt="image-20241021192012301"  />

即，字符串可以使用+号和任意数据类型做拼接，形成新的字符串。

```java
public class StringTest4 {
    public static void main(String[] args) {
        String s1 = "abc";
        String s2 = "a" + "b" + "c";
        System.out.println(s1 == s2); //true
    }
}
```

注：test3中是字符串变量和字符串常量做拼接。而test4是字符串常量拼接。**而java中有常量优化机制，即在编译阶段就会把上述三个常量拼接成一个整体。**

## 关于字符串的小扩展：

1. 字符串存储的内存原理

   String s = “abc”；直接赋值

   特点：

   ​	此时字符串abc是存在字符串常量池中的。

   ​	先检查字符串常量池中有没有字符串abc，如果有，不会创建新的，而是直接复用。如果没有abc，才会创建一个新的。

   所以，直接赋值的方式，代码简单，而且节约内存。

2. new出来的字符串

   看到new关键字，一定是在堆里面开辟了一个小空间。

   String s1 = new String（“abc”）；

   String s2 = “abc”；

   s1记录的是new出来的，在堆里面的地址值。

   s2是直接赋值的，所以记录的是字符串常量池中的地址值。

3. ==号比较的到底是什么？

   如果比较的是基本数据类型：比的是具体的数值是否相等。

   如果比较的是引用数据类型：比的是地址值是否相等。

   结论：==只能用于比较基本数据类型。不能比较引用数据类型。

## 字符串比较

```java
public boolean  equals方法(要比较的字符串)              // 完全一样结果才是true，否则为false 
public boolean  equalsIgnoreCase(要比较的字符串)		// 忽略大小写的比较
```

## 字符串遍历

```java
public char[]  toCharArray ()    //将字符串转换为字符数组 
public char chatAt (int index)  //根据索引找字符
public int length()  //返回字符串的长度
```

## 字符串的截取方法

```java
public String substring(int beginIndex, int endIndex)   //  根据传入的开始和结束索引对字符串做截取。（注意点：包头不包尾，包左不包右）
      
public String substring(int beginIndex)    //根据传入的索引开始做截取，截取到字符串末尾

```

## 字符串的替换方法

```java
public String replace(旧值,新值)  // 注意点：返回值才是替换之后的结果
```

## 字符串的切割方法

```java
public String[] split(String regex)
//根据传入的字符串作为规则进行切割
//将切割后的内容存入字符串数组中，并将字符串数组返回
```

# StringBuilder类

提高字符串操作效率。

StringBuilder是字符串的缓冲区, 我们可以将其理解为是一种容器。

 StringBuilder是一种可变的字符序列。

## 构造方法

> | 构造方法                         | 说明                                           |
> | -------------------------------- | ---------------------------------------------- |
> | public StringBuilder()           | 创建一个空的字符串缓冲区(容器)                 |
> | public StringBuilder(String str) | 创建一个字符串缓冲区, 并初始化好指定的参数内容 |

## 常用方法

> | 方法名                                 | 说明                                                |
> | -------------------------------------- | --------------------------------------------------- |
> | public StringBuilder append (任意类型) | 添加数据，并返回对象本身                            |
> | public StringBuilder reverse()         | 反转容器中的内容                                    |
> | public int  length()                   | 返回长度 ( 字符出现的个数)                          |
> | public String toString()               | 通过toString()就可以实现把StringBuilder转换为String |

# Static

可以修饰成员变量，也可以修饰成员方法

## 特点

1.被其修饰的成员, 被该类的所有对象所共享

2.多了一种调用方式, 可以通过类名调用

3.随着类的加载而加载, 优先于对象存在

![image-20240702104200046](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/TyporaPic/image-20240702104200046.png)

**注：静态方法里面不能访问非静态。只能访问静态成员（直接访问）**（因为，当静态随着类的加载而加载时，非静态的还不存在）

<img src="https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/TyporaPic/image-20241023162746355.png" alt="image-20241023162746355"  />

![image-20240702104720997](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/typora-user-images/image-20240702104720997.png)

![image-20240702104810216](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/typora-user-images/image-20240702104810216.png)

**注：static中不允许使用this关键字**（this代表的是当前对象的引用，而static随着类的加载而加载, 优先于对象存在）

## main

**public static void main(String[] args)**

- public： 被JVM调用，访问权限足够大
- static： 被JVM调用，不用创建对象
  -   因为main方法是静态的，所以main中调用其他方法也需要是静态的

- void： 被JVM调用，不需要给JVM返回值
- main： 一个通用的名称，虽然不是关键字，但是被JVM识别
- String[] args:  以前用于接收键盘录入数据的，现在没用

# 继承

1.子类可以直接使用父类中**非私有的成员**

2.子类需要自己手动编写构造方法，不能继承父类的构造方法，但是可以通过super调用。

3.子类的每个构造方法的第一行（必须在第一行），有一个默认的super()（可以省略不写）。所以子类所有构造方法的第一行都会默认先调用父类的无参构造方法。

4.如果想调用父类的有参构造给成员变量赋值，就必须手动书写。手动调用父类构造会覆盖默认的super()。

5.用this访问本类中的其他构造。这里的this()和super()一样，也必须写在第一行。super() 和 this() 都必须是在构造方法的第一行，所以不能同时出现。

![image-20240702112156970](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/typora-user-images/image-20240702112156970.png)

此时，虚拟机在无参构造中就不会再添加super()，因为在调用的有参构造的第一行有默认省略的super()。所以，在子类的构造方法中，有一个super()去访问父类就可以了。

6.子父类中，如果出现了重名的成员变量，根据就近原则，优先使用子类的。如果要用父类的，通过super关键字进行调用。

7.**父类中的私有方法不能被子类重写。**

8.子类重写父类方法时，访问权限必须大于等于父类

# 权限修饰符

![image-20241023165721271](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/TyporaPic/image-20241023165721271.png)

# final

## 特点

- 修饰方法：表明该方法是最终方法，**不能被重写**
- 修饰类：表明该类是最终类，**不能被继承**
- 修饰变量：表明该变量是常量，**不能再次被赋值**

> 注：
>
> - 变量是基本类型：final 修饰指的是基本类型的**数据值**不能发生改变
>
> - 变量是引用类型：final 修饰指的是引用类型的**地址值**不能发生改变，但是地址里面的内容是可以发生改变的
>
> - 成员变量如果被 final 修饰，需要在构造方法结束之前完成赋值

# abstract

- 抽象类：如果一个类中存在抽象方法，那么该类就必须声明为抽象类。
  -  定义格式：

```java
public abstract class 类名{}
```

- 抽象方法：将共性的行为（方法）抽取到父类之后，发现该方法的实现逻辑无法在父类中给出具体明确，该方法就可以定义为抽象方法。
  -  定义格式

```java
public abstract 返回值类型 方法名(参数列表);
```

**注**：

1. 抽象类不能实例化。
2. 抽象类存在构造方法。
3. 抽象类中可以存在普通方法。
4. 抽象类的子类：要么重写抽象类中的所有抽象方法，要么是抽象类。

**abstract关键字冲突问题**：

1. final：被 abstract 修饰的方法，强制要求子类重写，但**被 final 修饰的方法子类不能重写**
2. private：被 abstract 修饰的方法，强制要求子类重写，但**被 private 修饰的方法子类不能重写**
3. static：被 static 修饰的方法可以类名调用，类名调用抽象方法没有意义

# 接口

1.接口不允许实例化。（因为接口中所有方法都是抽象方法，如果允许new出对象，那么对象去调那么抽象方法是没意义的）

2.接口的实现类：要么重写接口中的所有抽象方法，要么是抽象类。

## 成员

- 成员变量
  -  只能定义常量，因为系统会默认加入修饰符：**public static final**

- 成员方法
  - 只能是抽象方法，因为系统会默认加入修饰符：**public abstract**

- **没有构造方法**

## 抽象类和接口的对比

- 成员变量 :
  - 抽象类 : 可以定义变量, 也可以定义常量
  - 接口 : 只能定义常量 

- 成员方法
  - 抽象类 : 可以定义具体方法, 也可以定义抽象方法
  - 接口 : 只能定义抽象方法

- 构造方法
  - 抽象类 : 有
  - 接口 : 没有

## 新特性

### JDK8的新特性：

1、接口中可以定义有方法体的方法， 即非抽象方法，但需要使用关键字default修饰。

> **注：**
>
> 1.public可以省略, 但是default不能省略；
>
> 2.这种方法不是抽象方法，所以不强制实现类重写，但允许实现类重写, 但是实现类在重写的时候不能写default关键字；
>
> 3.如果实现了多个接口, 多个接口中存在相同的默认方法, **实现类必须重写默认方法**；

```java
super.method();//这是找直接父类的method方法，即这个类继承了哪个类，哪个类就是它的直接父类
Inter.super().method();//这是找这个类实现的接口Inter中的method方法
```

```java
//如：
default void function(){
    ...
}
```

2、允许定义静态方法

> **注：**
>
> 1.public可以省略, 但是static不能省略
>
> 2.接口中的静态方法, 只允许接口名进行调用, 不允许实现类通过对象调用

```java
//如：
static void function(){
    ...
}
```

### JDK9的新特性：

接口中可以定义私有方法。

> **注：**
>
> 接口中的私有方法不能带default

```java
//如：
private void function(){
    ...
}
```

# 多态

## 前提

1.有继承 / 实现关系。

2.有方法重写。

3.有父类引用指向子类对象。（比如，Animal a = new Dog()，左边是右边的父类。Dog d = new Dog()，这个叫子类引用指向子类对象）

## 多态的成员访问特点

**成员变量**：编译看左边（父类），执行看左边（父类）

**成员方法**：编译看左边（父类），执行看右边（子类）

## 多态中的转型问题

### **ClassCastException**

在引用数据类型的强转中，[实际类型] 和 [目标类型]不匹配，就会出现此异常

```java
OrderService orderService = new OrderServiceImpl();
OverseaServiceImpl osi = (OverseaServiceImpl)orderService; //此时就会出现ClassCastException
//因为，orderService的实际类型是OrderServiceImpl，而它强转的目标类型是OverseaServiceImpl
```

### instanceof

使用格式：对象名 instanceof 类型

判断一个对象是否是一个类的实例。即，判断关键字左边的对象，是否是右边的类型，返回boolean类型结果

# 代码块

## 局部代码块

位置：方法中定义

作用：限定变量的生命周期，及早释放，提高内存利用率

## 构造代码块

位置：类中方法外定义

特点：每次构造方法执行的时，都会执行该代码块中的代码，并且在构造方法执行前执行

作用：将多个构造方法中相同的代码，抽取到构造代码块中，提高代码的复用性

## 静态代码块

位置：类中方法外定义

特点：需要通过static关键字修饰，随着类的加载而加载，并且只执行一次

作用：在类加载的时候做一些数据初始化的操作

```java
class Student {

    static String school;
    
    {
        System.out.println("Student类的构造代码块");
    }

    static {
        school = "黑马程序员";
        System.out.println("static...Student类的静态代码块");

        // Socket
    }

    {
        System.out.println("Student类的构造代码块");
        System.out.println("好好学习");
    }

    public Student(){

        System.out.println("空参构造方法...");
    }

    public Student(int num){
        System.out.println("带参构造方法...");
    }

}
```

# 内部类：

## 成员内部类：

访问特点：

内部类可以直接访问外部类的成员，包括私有。

外部类要访问内部类的成员，必须创建对象。

创建对象的格式：

```java
//格式：外部类名.内部类名 对象名 = new 外部类对象().new 内部类对象();
//范例：Outer.Inner in = new Outer().new Inner();
class Outer {
    // 内部类
    class Inner {
    }
}

//这样的格式是为了区分你的内部类是哪个外部类的
class Test {
    // 内部类
    class Inner {
    }
}

//如果此时有两个Inner，若用Inner in = new Inner();，则无法区分是要创建Outer的Inner类还是Test的Inner类。
```

```java
public class Car {

    String carName;
    int carAge;
    String carColor;

    public void show(){
        System.out.println(carName);
        Engine e = new Engine();          //此时没有使用上述的完整格式创建。此时在这个外部类的里面创建的这个类，那么找的就是我这个类里面的Engine类 
        System.out.println(e.engineName); //外部类要访问内部类的成员，必须创建对象。
    }

    class Engine{
        String engineName;
        int engineAge;

        public void show(){
            System.out.println(engineName);
            System.out.println(carName); //内部类可以直接访问外部类的成员，包括私有。
        }
    }
}
```

## 面试题

```java
class Outer {
    int num = 150;
    class Inner {
        int num = 110;
        public void show(){
            int num = 78;
            System.out.println(num); // 78
            System.out.println(this.num); // 110
            System.out.println(Outer.this.num); // 150   在成员内部类中访问所在外部类对象 ，格式：外部类名.this
            									//此时如果使用上述规则“内部类可以直接访问外部类的成员，包括私有。”，
            									//直接写num，那只能输出78。 直接写this.num，那只能输出110。
        }
    }
}

```

**编写成员内部类的注意点：**

1.成员内部类可以被一些修饰符修饰，比如：private，默认，protected，public，static等。

2.在成员内部类里面，JDK16之前不能定义静态变量，JDK16开始才可以定义静态变量。

**获取成员内部类的两种方式：**

方式一：外部直接创建成员内部类的对象

```java
public class Outer {
    class Inner{

    }
}

public class demo1 {
    public static void main(String[] args) {
        Outer.Inner oi = new Outer().new Inner(); // 外部类.内部类 变量 = new 外部类（）.new 内部类（）;
    }
}
```

方式二：在外部类中定义一个方法提供内部类的对象

```java
//当内部类被private修饰时，无法直接使用Outer.Inner。此时可以在外部类中定义一个方法提供内部类的对象
public class Outer {
    private class Inner{

    }
    public Inner getInstance(){
        return new Inner();
    }
}

public class demo1 {
    public static void main(String[] args) {
        Outer o = new Outer();
        Object oi = o.getInstance();
        System.out.println(o.getInstance());

    }
}
```

## 静态内部类：

![image-20240702194639588](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/typora-user-images/image-20240702194639588.png)

静态内部类也是成员内部类的一种。它只能访问外部类中的静态变量和静态方法，如果想要访问非静态的需要创建对象。 

```java
public class Outer {
    static class Inner{
        public void show1(){
        }
        public static void show2(){
        }
    }
}	

public class demo1 {
    public static void main(String[] args) {
        //创建静态内部类对象的格式： 外部类名.内部类名 对象名 = new 外部类名.内部类名();
        Outer.Inner oi = new Outer.Inner();
        oi.show1();
        //调用静态方法的格式： 外部类名.内部类名.方法名();
        Outer.Inner.show2();
    }
}
```

##  局部内部类：

1.将内部类定义在方法里面就叫做局部内部类，类似于方法里面的局部变量

2.外界无法直接使用，需要在方法内部创建对象并使用

3.该类可以直接访问外部类的成员,也可以访问方法内的局部变量

## 匿名内部类:

```java
new 类名或者接口名() {
     重写方法;
};
//new 类名() {} ： 代表继承这个类
//new 接口名() {} ： 代表实现这个接口
```

**使用方式:以接口为例**

```java
interface Swim {
    public abstract void swimming();
}

public class Demo07 {
    public static void main(String[] args) {
        // 使用匿名内部类
		new Swim() {
			@Override
			public void swimming() {
				System.out.println("自由泳...");
			}
		}.swimming();

        // 接口 变量 = new 实现类(); // 多态
        Swim s2 = new Swim() {
            @Override
            public void swimming() {
                System.out.println("蛙泳...");
            }
        };
        s2.swimming();
    }
}
```

**使用场景:**

```java
interface Swim {
    public abstract void swimming();
}

// 1. 定义接口的实现类
class Student implements Swim {
    // 2. 重写抽象方法
    @Override
    public void swimming() {
        System.out.println("狗刨式...");
    }
}

public class Demo07 {
    public static void main(String[] args) {
        // 普通方式传入对象
        // 创建实现类对象
        Student s = new Student();
        
        goSwimming(s);
        
        // 匿名内部类使用场景:作为方法参数传递
        Swim s3 = new Swim() {
            @Override
            public void swimming() {
                System.out.println("蝶泳...");
            }
        };
        // 传入匿名内部类
        goSwimming(s3);

        // 完美方案: 一步到位
        goSwimming(new Swim() {
            public void swimming() {
                System.out.println("大学生, 蛙泳...");
            }
        });

        goSwimming(new Swim() {
            public void swimming() {
                System.out.println("小学生, 自由泳...");
            }
        });
    }

    // 定义一个方法,模拟请一些人去游泳
    public static void goSwimming(Swim s) {
        s.swimming();
    }
}
```

# Lambda表达式

Lambda表达式是 JDK8 开始后的一种新语法形式。作用：简化匿名内部类的代码写法。

格式：

> (匿名内部类被重写方法的形参列表) -> {
>     被重写方法的方法体代码。
> }

Lambda 表达式只能**简化函数式接口的匿名内部类**的写法形式。

> **什么是函数式接口？**
>
> - 首先必须是接口、其次接口中有且仅有一个抽象方法的形式
> - 通常我们会在接口上加上一个@FunctionalInterface注解，标记该接口必须是满足函数式接口。

省略写法：

> - 参数类型可以省略不写。
>
> - 如果只有一个参数，参数类型可以省略，同时 () 也可以省略。
>
> - 如果Lambda表达式的方法体代码只有一行代码
>
>   可以省略大括号不写,同时要省略分号
>
>   此时，如果这行代码是return语句，必须省略return不写，同时也必须省略 ";" 不写

# 常用API

## Object类

所有的类，都直接或者间接的继承了 Object 类 （祖宗类）

Object类的方法是一切子类都可以直接使用的，所以要学习Object类的方法。

### 常用方法：

| 方法名                          | 说明                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| public String toString()        | 默认是返回当前对象在堆内存中的地址信息:类的全类名@内存地址  (十六进制哈希值) |
| public Boolean equals(Object o) | 默认是比较当前对象与另一个对象的地址是否相同，相同返回true，不同返回false |

**toString**存在的意义

父类 toString() 方法存在的意义就是为了被子类重写，以便返回对象的内容信息，而不是地址信息！！

```java
//源码
public String toString() {
       return getClass().getName() + "@" + Integer.toHexString(hashCode());
}

// getClass().getName() : 类名称, 全类名(包名 + 类名)
// Integer.toHexString() : 转十六进制
// hashCode() : 返回的是对象内存地址 + 哈希算法, 算出来的整数 (哈希值)
```

> 使用打印语句, 打印对象名的时候, println方法, 源码层面, 会自动调用该对象的toString方法
>
> ```java
> // System.out.println(student);
> //源码
> public static String valueOf(Object obj) {
>        return (obj == null) ? "null" : obj.toString();
> }
> ```

**equals**存在的意义

父类equals()方法存在的意义就是为了被子类重写，以便子类自己来定制比较规则。

```java
// idea快捷键生成的重写的equals方法
    @Override
    public boolean equals(Object o) {
        // this : stu1
        // o : stu2
        if (this == o) {
            // 两个对象做地址值的比较, 如果地址相同, 里面的内容肯定相同, 直接返回为true.
            return true;
        }

        // 代码要是能够走到这里, 代表地址肯定不相同
        // 代码要是能够走到这里, 代表stu1, 肯定不是null
        // stu1不是null, stu2是null, 就直接返回false

        // this.getClass() != o.getClass() : 两个对象的字节码是否相同
        // 如果字节码不相同, 就意味着类型不相同, 直接返回false
        if (o == null || this.getClass() != o.getClass()) {
            return false;
        }

        // 代码要是能够走到这里, 代表字节码相同, 类型肯定相同.
        // 向下转型
        Student student = (Student) o;
        // 比较
        return this.age == student.age && Objects.equals(this.name, student.name);
    }
```

## Objects类

Objects类与 Object 还是继承关系，Objects类是从JDK 1.7开始之后才有的。

### 常用方法：

| 方法名                                             | 说明                                                         |
| -------------------------------------------------- | ------------------------------------------------------------ |
| public  static boolean equals(Object a,  Object b) | 比较两个对象的，底层会先进行非空判断，从而可以避免空指针异常。再进行equals比较 |
| public  static boolean isNull(Object obj)          | 判断变量是否为 null                                          |

> 问题: Objects.equals方法, 和 stu1.equals方法, 有什么区别?
>
> 细节: Objects.equals方法, 内部依赖于我们自己所编写的equals
>
> 好处: Objects.equals方法, 内部带有非null判断

```java
Student stu1 = null;
Student stu2 = new Student("张三",23);
Objects.equals(stu1,stu2);

//源码
       /*
                                        // a : stu1
                                        // b : stu2
            public static boolean equals(Object a, Object b) {
                return (a == b) || (a != null && a.equals(b));
            }
            
                -----------------------------------------------------------------------------------------------
                a == b : 如果地址相同, 就会返回为true, 这里使用的符号是短路 || , 左边为true, 右边就不执行了
                            - 结论: 如果地址相同, 方法直接返回为true

                       : 如果地址不相同, 就会返回false, 短路 || , 左边为false, 右边要继续执行.

                -----------------------------------------------------------------------------------------------

                a != null : 假设 a 是 null 值

                                        null != null  :  false
                                        && : 左边为false, 右边不执行, 右边不执行, 记录着null值的a, 就不会调用equals方法
                                                    - 避免空指针异常 !

                -----------------------------------------------------------------------------------------------

                a != null : 假设 a 不是 null 值

                                        stu1 != null : true
                                        && : 左边为true, 右边继续执行, a.equals(b), 这里就不会出现空指针异常
                                        
         */
```

## Math类

包含执行**基本数字运算**的方法

### 常用方法：

| 方法名                                      | 说明                                  |
| ------------------------------------------- | ------------------------------------- |
| public static int abs(int a)                | 获取参数绝对值                        |
| public static double  ceil(double a)        | 向上取整                              |
| public static double  floor(double a)       | 向下取整                              |
| public static int round(float a)            | 四舍五入                              |
| public static int max(int a,int b)          | 获取两个int值中的较大值               |
| public static double pow(double a,double b) | 返回a的b次幂的值                      |
| public static double random()               | 返回值为double的随机值，范围[0.0,1.0) |

## System类

System的功能是**静态**的，都是直接用类名调用即可

### 常用方法：

| 方法名                                                       | 说明                                         |
| ------------------------------------------------------------ | -------------------------------------------- |
| public  static void exit(int status)                         | 终止当前运行的 Java 虚拟机，非零表示异常终止 |
| public  static long currentTimeMillis()                      | 返回当前系统的时间毫秒值形式                 |
| public  static void arraycopy(数据源数组, 起始索引, 目的地数组, 起始索引, 拷贝个数) | 数组拷贝                                     |

## BigDecimal类

用于解决小数运算中, 出现的不精确问题

```java
public class BigDecimalDemo {
    public static void main(String[] args) {
        double num1 = 0.1;
        double num2 = 0.2;
        System.out.println(num1 + num2); // 0.30000000000000004
    }
}
```

### 创建对象：

| public BigDecimal(double val)                |
| -------------------------------------------- |
| public BigDecimal(String val)                |
| public static BigDecimal valueOf(double val) |

### 常用方法：

| 方法名                                                       | 说明 |
| ------------------------------------------------------------ | ---- |
| public BigDecimal add(BigDecimal b)                          | 加法 |
| public BigDecimal subtract(BigDecimal b)                     | 减法 |
| public BigDecimal multiply(BigDecimal b)                     | 乘法 |
| public BigDecimal divide(BigDecimal b)                       | 除法 |
| public BigDecimal divide (另一个BigDecimal对象，精确几位，舍入模式) | 除法 |

```java
BigDecimal divide = bd1.divide(参与运算的对象, 小数点后精确到多少位, 舍入模式);
//参数1 ，表示参与运算的BigDecimal 对象。
//参数2 ，表示小数点后面精确到多少位
//参数3 ，舍入模式

//RoundingMode.UP  进一法
//RoundingMode.DOWN  去尾法
//RoundingMode.HALF_UP 四舍五入

```

## 包装类

将**基本数据类型**, 包装成**类** (变成引用数据类型)。变成类, 就可以创建对象了。对象就可以调用方法方便的解决问题了。

用一个对象，把基本数据类型给包起来。

所有的对象都可以用Object接收，但不能接收基本数据类型。并且，在集合中，不能存储基本数据类型，只能存对象。

![image-20240703143143197](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/typora-user-images/image-20240703143143197.png)

| 基本类型 | 对应的包装类（位于java.lang包中） |
| :------: | :-------------------------------: |
|   byte   |               Byte                |
|  short   |               Short               |
|   int    |            **Integer**            |
|   long   |               Long                |
|  float   |               Float               |
|  double  |              Double               |
|   char   |           **Character**           |
| boolean  |              Boolean              |

### Integer

**获取Integer对象的方式：**

|                       方法名                       |                   说明                    |
| :------------------------------------------------: | :---------------------------------------: |
|             public Integer(int value)              |    根据 int 值创建 Integer 对象(过时)     |
|              public Integer(String s)              |   根据 String 值创建 Integer 对象(过时)   |
|        public static Integer valueOf(int i)        |     根据传递的整数创建一个Integer对象     |
|      public static Integer valueOf(String s)       |    根据传递的字符串创建一个Integer对象    |
| public static Integer valueOf(String s, int radix) | 根据传递的字符串和进制创建一个Integer对象 |

**两种方式获取对象的区别：**

```java
//底层原理：
//因为在实际开发中，-128~127之间的数据，用的比较多。
//如果每次使用都是new对象，那么太浪费内存了
//所以，提前把这个范围之内的每一个数据都创建好对象
//如果用到了这个范围内的数，不会创建新的，而是返回已经创建好的对象。
Integer i6 = Integer.valueOf(127);
Integer i7 = Integer.valueOf(127);
System.out.println(i6 == i7);//true

Integer i8 = Integer.valueOf(128);
Integer i9 = Integer.valueOf(128);
System.out.println(i8 == i9);//false

//因为看到了new关键字，在Java中，每一次new都是创建了一个新的对象
//所以下面的两个对象都是new出来，地址值不一样。
Integer i10 = new Integer(127);
Integer i11 = new Integer(127);
System.out.println(i10 == i11);//false

Integer i12 = new Integer(128);
Integer i13 = new Integer(128);
System.out.println(i12 == i13);//false
```

**自动装箱和自动拆箱：**

自动装箱：从基本类型自动转换为对应的包装类对象。

自动拆箱：从包装类对象自动转换为对应的基本类型。

```java
Integer i = 4;//自动装箱。相当于Integer i = Integer.valueOf(4);
i = i + 5;//等号右边：将i对象转成基本数值(自动拆箱) i.intValue() + 5;
//加法运算完成后，再次装箱，把基本数值转成对象。
```

### 常用方法

| 方法名                                     | 说明                                |
| ------------------------------------------ | ----------------------------------- |
| public static String toBinaryString(int i) | 得到二进制                          |
| public static String toOctalString(int i)  | 得到八进制                          |
| public static String toHexString(int i)    | 得到十六进制                        |
| public static int parseInt(String s)       | 将字符串类型的整数转成int类型的整数 |

## Arrays

数组操作工具类，专门用于操作数组元素

### 常用方法：

| 方法名                                             | 说明                                |
| -------------------------------------------------- | ----------------------------------- |
| public static String toString(类型[] a)            | 将数组元素拼接为带有格式的字符串    |
| public  static boolean equals(类型[] a,  类型[] b) | 比较两个数组内容是否相同            |
| public  static int binarySearch(int[] a,  int key) | 查找元素在数组中的索引 (二分查找法) |
| public  static void sort(类型[] a)                 | 对数组进行默认升序排序              |

方法：

```java
//public static int binarySearch(数组，查找元素)  二分查找法查找元素

		int[] arr = {1,2,3,4,5,6,7,8,9,10};
        System.out.println(Arrays.binarySearch(arr,10)); //9
        System.out.println(Arrays.binarySearch(arr,2)); //1
        System.out.println(Arrays.binarySearch(arr,20)); //-11
//细节一：二分查找法的前提是：数组中的元素必须是有序的。binarySearch方法还要求，数组中的元素必须是升序。
//细节二：如果查找的元素是存在的，那么返回的是真实的索引
//如果查找的元素不存在，返回的是 -插入点-1
//问：为什么要-1呢？
//答：如果此时要查找数字0，那么如果返回的值是 -插入点，就会出现问题。此时0是不存在的，就会返回-0，即0。那么就无法分辨出这个0是元素0，还是不存在的0。所以要-1。
```

```java
//public static int[] copyOf(原数组,新数组长度)  拷贝数组
//方法的底层会根据第二个参数来创建新的数组
//如果新数组的长度是小于老数组的长度，会部分拷贝
//如果新数组的长度是等于老数组的长度，会完全拷贝
//如果新数组的长度是大于老数组的长度，会补上默认初始值

		int[] arr = {1,2,3,4,5,6,7,8,9,10};
		int[] newArr1 = Arrays.copyOf(arr,2);
		System.out.println(Arrays.toString(newArr1)); //[1,2]
		int[] newArr1 = Arrays.copyOf(arr,10);
		System.out.println(Arrays.toString(newArr1)); //[1,2,3,4,5,6,7,8,9,10]
		int[] newArr1 = Arrays.copyOf(arr,15);
		System.out.println(Arrays.toString(newArr1)); //[1,2,3,4,5,6,7,8,9,10,0,0,0,0,0]
```

```java
//public static int[] copyOfRange(原数组,起始索引,结束索引)  拷贝数组（指定范围）
//细节：包头不包尾，包左不包右

		int[] arr = {1,2,3,4,5,6,7,8,9,10};
		int[] newArr2 = Arrays.copyOfRange(arr,0,9);
		System.out.println(Arrays.toString(newArr2)); //[1,2,3,4,5,6,7,8,9]
```

```java
//public static void sort(数组)  按照默认方式进行数组排序
//细节：默认情况下，给基本数据类型进行升序排序，底层使用的是快速排序
//public static void sort(数组,排序规则)  按照指定规则数组排序

		int[] arr = {10,2,3,5,6,1,7,8,4,9};
		Arrays.sort(arr);
		System.out.println(Arrays.toString(arr)); //[1,2,3,4,5,6,7,8,9,10]
```

# 正则表达式

本质来说就是一个字符串, 可以指定一些规则, 来校验其他字符串.

<img src="https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/TyporaPic/image-20241031165913106.png" alt="image-20241031165913106" style="zoom: 50%;" />

## 字符类(默认匹配一个字符)

```java
        [abc]	       只能是a, b, 或c
        [^abc]	       除了a, b, c之外的任何字符
        [a-zA-Z]       a到z A到Z，包括（范围）
        [a-d[m-p]]	   a到d，或m到p （等价于[a-dm-p]）
        [a-z&&[def]]   d, e, 或f(交集)   也可以直接写[def]，这里只是了解下&&这种规则
        [a-z&&[^bc]]   a到z，除了b和c （等价于[ad-z]）
        [a-z&&[^m-p]]  a到z，除了m到p （等价于[a-lq-z]）
```

## 预定义字符类(默认匹配一个字符)

```java
		.	任何字符
        \d	一个数字： 等价于[0-9]           要写成 String regex = "\\d";      
        \D	非数字： [^0-9]
        \s	一个空白字符： [ \t\n\x0B\f\r]
        \S	非空白字符： [^\s]
        \w	[a-zA-Z_0-9] 英文、数字、下划线
        \W	 [^\w] 一个非单词字符

        \ : 转义字符

        System.out.println("\t");           \ ---> t ---> 解密成功 ---> tab键
        System.out.println("\"");           // 输出 "  
        System.out.println('\'');           // 输出 '

```

## 贪婪的量词（配合匹配多个字符）

```java
		X?	, 一次或0次
        X*	, 零次或多次 (任意次数)
        X+	, 一次或多次
        X{n}	，正好n次
        X{n, }	，至少n次
        X{n,m}	，至少n但不超过m次
```

## String类中与正则有关的常见方法

| 方法名                                               | 说明                                                       |
| ---------------------------------------------------- | ---------------------------------------------------------- |
| public String replaceAll(String regex,String newStr) | 按照正则表达式匹配的内容进行替换                           |
| public String[] split(String regex)                  | 按照正则表达式匹配的内容进行分割字符串，反回一个字符串数组 |

# 时间API

## JDK8(-)

### Data类

代表日期和时间

| 构造器                 | 说明                                                         |
| ---------------------- | ------------------------------------------------------------ |
| public Date()          | 创建一个Date对象，代表的是系统当前此刻日期时间。             |
| public Date(long time) | 把时间毫秒值转换成Date日期对象。  从1970年1月1日   00:00:00换算 |

| 常见方法                       | 说明                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| public long getTime()          | 返回从1970年1月1日   00:00:00走到此刻调用的date对象的总的毫秒数 |
| public void setTime(long time) | 设置此刻调用date对象的时间为当前参数时间毫秒值对应的时间 。从1970年1月1日   00:00:00换算 |

```java
        Date date = new Date();
        System.out.println(date);//Wed Nov 13 11:06:37 CST 2024

        Date date1 = new Date(0L);
        System.out.println(date1);//Thu Jan 01 08:00:00 CST 1970

        Date date2 = new Date(1000L);
        System.out.println(date2);//Thu Jan 01 08:00:01 CST 1970

        System.out.println(date.getTime());//1731467489303
        System.out.println(date1.getTime());//0

        date.setTime(0L);
        System.out.println(date);//Thu Jan 01 08:00:00 CST 1970
```

### SimpleDataFormat类

用于日期格式的简化

| 构造器                                  | 说明                                     |
| --------------------------------------- | ---------------------------------------- |
| public  SimpleDateFormat()              | 构造一个SimpleDateFormat，使用默认格式   |
| public SimpleDateFormat(String pattern) | 构造一个SimpleDateFormat，使用指定的格式 |

| 格式化方法                                 | 说明                          |
| ------------------------------------------ | ----------------------------- |
| public final String **format(Date date)**  | 将日期格式化成日期/时间字符串 |
| public final Date **parse(String source)** | 将字符串解析为日期类型        |

```java
SimpleDateFormat simpleDateFormat = new SimpleDateFormat();
Date date = new Date();

String result = simpleDateFormat.format(date);
System.out.println(result);//24-11-13 下午2:22

SimpleDateFormat simpleDateFormat1 = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss E a");//格式意思查api文档
String result1 = simpleDateFormat1.format(date);
System.out.println(result1);//2024年11月13日 14:22:19 星期三 下午
        
String today = "2024年11月13日";//得和下面的模式匹配
SimpleDateFormat simpleDateFormat2 = new SimpleDateFormat("yyyy年MM月dd日");//Wed Nov 13 00:00:00 CST 2024
Date date1 = simpleDateFormat2.parse(today);
System.out.println(date1);
```

### Calendar类

代表的是系统此刻时间对应的日历，通过它可以单独获取、修改时间中的年、月、日、时、分、秒等。

创建对象 : Calendar是一个抽象类，不能直接创建对象。所以Calendar.getInstance()获取的是子类对象。

| 方法名                                | 说明                   |
| ------------------------------------- | ---------------------- |
| public  static Calendar getInstance() | 获取当前时间的日历对象 |

常用方法：

| 方法名                                | 说明                        |
| ------------------------------------- | --------------------------- |
| public int get(int field)             | 取日历中的某个字段信息。    |
| public void set(int field,int value)  | 修改日历的某个字段信息      |
| public void add(int field,int amount) | 为某个字段增加/减少指定的值 |

> *get方法的参数 : Calendar类中的静态常量*
>
> ​                  *Calendar.YEAR : 获取年份信息*
>
> ​                  *Calendar.MONTH : 月份是0~11, 记得做+1操作*
>
> ​                  *Calendar.DAY_OF_MONTH : 获取日*
>
> ​                  *Calendar.DAY_OF_WEEK : 获取星期, 获取星期的时候, 需要提前设计一个数组*
>
> ​                                                              char[] weeks = {' ', '日', '一', '二', '三', '四', '五', '六'}
>
> ​                  *Calendar.DAY_OF_YEAR : 获取一年中的第几天*

## JDK8(+)

```java
		// JDK8版本之前 :
        // 都是可变对象, 修改后会丢失最开始的时间信息
        Date time = new Date();
        System.out.println("修改前获取时间: " +  time);
        // 修改
        time.setTime(1000);
        System.out.println("修改后获取时间:" +  time);

        System.out.println("--------------------");

        // JDK8版本之后 :
        // 都是不可变对象, 修改后会返回新的时间对象, 不会丢失最开始的时间
        LocalDateTime now = LocalDateTime.now();
        System.out.println("修改前获取时间:" + now);

        LocalDateTime afterTime = now.withYear(2008);
        System.out.println("修改后获取时间: " + afterTime);
        System.out.println("修改后获取时间: " + now);

------------------------------------------------------------------------------------------------------------
    修改前获取时间: Wed Nov 13 15:18:53 CST 2024
	修改后获取时间:Thu Jan 01 08:00:01 CST 1970
	--------------------
	修改前获取时间:2024-11-13T15:18:54.018
	修改后获取时间: 2008-11-13T15:18:54.018
	修改后获取时间: 2024-11-13T15:18:54.018
```

### 日历类

LocalDate：代表本地日期(年、月、日、星期)

LocalTime：代表本地时间(时、分、秒、纳秒)

LocalDateTime：代表本地日期、时间(年、月、日、星期、时、分、秒、纳秒)

**获取对象**：

```java
  public static Xxxx now(): 获取系统当前时间对应的该对象 
       示例：
      LocaDate ld = LocalDate.now();     
	  LocalTime lt = LocalTime.now();     
	  LocalDateTime ldt = LocalDateTime.now();  
  public static Xxxx of(…)：获取指定时间的对象
      示例：
      LocalDate localDate1 = LocalDate.of(2099 , 11,11); 
	  LocalTime localTime1 = LocalTime.of(9, 8, 59); 
	  LocalDateTime localDateTime1 = LocalDateTime.of(2025, 11, 16, 14, 30, 01);
```

**获取年月日时分秒相关的方法**：

| 方法名                   | 功能                             |
| ------------------------ | -------------------------------- |
| int getYear()            | 获取年份字段                     |
| Month getMonth()         | 使用 Month 枚举获取年份字段      |
| int getMonthValue()      | 获取1到12之间的月份字段          |
| int getDayOfMonth()      | 获取日期字段                     |
| DayOfWeek getDayOfWeek() | 获取星期几字段，即枚举 DayOfWeek |
| int getHour()            | 获取当日时间字段                 |
| int getMinute()          | 获取分钟字段                     |
| int getSecond()          | 获取第二分钟字段                 |

```java
        // 日期 时间
        LocalDateTime nowDateTime = LocalDateTime.now();
        System.out.println("今天是：" + nowDateTime);
        // 年
        System.out.println(nowDateTime.getYear());
        // 月
        System.out.println(nowDateTime.getMonthValue());
        // 日
        System.out.println(nowDateTime.getDayOfMonth());
        // 时
        System.out.println(nowDateTime.getHour());
        // 分
        System.out.println(nowDateTime.getMinute());
        // 秒
        System.out.println(nowDateTime.getSecond());
        // 纳秒
        System.out.println(nowDateTime.getNano());

        // 日 : 当年的第几天
        System.out.println("dayOfYear：" + nowDateTime.getDayOfYear());
        // 星期
        System.out.println("星期" + nowDateTime.getDayOfWeek());
        System.out.println("星期" + nowDateTime.getDayOfWeek().getValue());
        // 月份
        System.out.println("月份" + nowDateTime.getMonth());
        System.out.println("月份" + nowDateTime.getMonth().getValue());

------------------------------------------------------------------------------------------------------------
    
今天是：2024-11-13T16:35:52.956
2024
11
13
16
35
52
956000000
dayOfYear：318
星期WEDNESDAY
星期3
月份NOVEMBER
月份11
```

**修改年月日时分秒相关的方法**：

LocalDateTime 、LocalDate 、LocalTime 都是不可变的, 下列方法返回的是一个新的对象

| **方法名**                                         | **说明**                                |
| -------------------------------------------------- | --------------------------------------- |
| withHour、withMinute、withSecond、withNano         | 修改时间，返回新时间对象                |
| plusHours、plusMinutes、plusSeconds、plusNanos     | 把某个信息加多少，返回新时间对象        |
| minusHours、minusMinutes、minusSeconds、minusNanos | 把某个信息减多少，返回新时间对象        |
| equals isBefore isAfter                            | 判断2个时间对象，是否相等，在前还是在后 |

**LocalDateTime** **转** **LocalDate** **和** **Local Time**：

| 方法名                         | 说明                    |
| ------------------------------ | ----------------------- |
| public LocalDate toLocalDate() | 转换成一个LocalDate对象 |
| public LocalTime toLocalTime() | 转换成一个LocalTime对象 |

### 日期格式化类

```java
 /*
        用于时间的格式化和解析:

        1. 对象的获取 :

                static DateTimeFormatter ofPattern(格式) : 获取格式对象

        2. 格式化 :

                String format(时间对象) : 按照指定方式格式化

        3. 解析 :

                LocalDateTime.parse("解析字符串", 格式化对象);
                LocalDate.parse("解析字符串", 格式化对象);
                LocalTime.parse("解析字符串", 格式化对象);

     */

        LocalDateTime now = LocalDateTime.now();
        System.out.println("格式化之前:" + now);

        // 获取格式化对象
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy年M月d日");

        // 格式化
        String result = formatter.format(now);
        System.out.println("格式化之后:" + result);

        // 解析
        String time = "2008年08月08日";
        LocalDate parse = LocalDate.parse(time, formatter);
        System.out.println(parse);
-------------------------------------------------------------------------------------------------
格式化之前:2024-11-13T16:44:25.502
格式化之后:2024年11月13日
2008-08-08
```

### 时间类

#### Instant类

用于表示时间的对象,  类似之前所学习的 Date

```java
Instant instant = Instant.now(); //是有时差的，需要结合下面的Zoneld类使用
```

**常见方法**：

```java
        Instant类常见方法 :

            static Instant now() : 获取当前时间的Instant对象（标准时间）
            static Instant ofXxxx(long epochMilli) : 根据（秒/毫秒/纳秒）获取Instant对象
            ZonedDateTime atZone(ZoneId zone) : 指定时区
            boolean isXxx(Instant otherInstant) : 判断系列的方法
            Instant minusXxx(long millisToSubtract) : 减少时间系列的方法
            Instant plusXxx(long millisToSubtract) : 增加时间系列的方法
                
public static void main(String[] args) {
        // 获取当前时间的Instant对象（标准时间）
        Instant now = Instant.now();
        System.out.println("当前时间为(世界标准时间):" + now);
        System.out.println("------------------");

        // 根据（秒/毫秒/纳秒）获取Instant对象
        Instant instant1 = Instant.ofEpochMilli(1000);//毫秒
        Instant instant2 = Instant.ofEpochSecond(5);//秒

        System.out.println(instant1);
        System.out.println(instant2);
        System.out.println("------------------");

        // 指定时区
        ZonedDateTime zonedDateTime = Instant.now().atZone(ZoneId.systemDefault());
        System.out.println("带时区的时间:" + zonedDateTime);
        System.out.println("------------------");

        // 判断系列的方法
        System.out.println(now.isBefore(instant1));
        System.out.println(now.isAfter(instant1));
        System.out.println("------------------");

        // 减少时间系列的方法
        System.out.println("减1000毫秒:" + now.minusMillis(1000));
        System.out.println("减5秒钟:" + now.minusSeconds(5));
        System.out.println("------------------");

        // 增加时间系列的方法
        System.out.println("加1000毫秒:" + now.plusMillis(1000));
        System.out.println("加5秒钟:" + now.plusSeconds(5));
        System.out.println("------------------");
    }
}
-----------------------------------------------------------------------------
当前时间为(世界标准时间):2024-11-13T09:07:41.212Z
------------------
1970-01-01T00:00:01Z
1970-01-01T00:00:05Z
------------------
带时区的时间:2024-11-13T17:07:41.248+08:00[Asia/Shanghai]
------------------
false
true
------------------
减1000毫秒:2024-11-13T09:07:40.212Z
减5秒钟:2024-11-13T09:07:36.212Z
------------------
加1000毫秒:2024-11-13T09:07:42.212Z
加5秒钟:2024-11-13T09:07:46.212Z
------------------
```

#### Zoneld类

**常见方法**：

```java
               1. static Set<String> getAvailableZoneIds() : 获取Java中支持的所有时区
               2. static ZoneId systemDefault() : 获取系统默认时区
               3. static ZoneId of(String zoneId) : 获取一个指定时区
                   
        // 获取Java中支持的所有时区
        Set<String> set = ZoneId.getAvailableZoneIds();
        System.out.println(set);
        System.out.println(set.size());
        System.out.println("-----------------------");

        // 获取系统默认时区
        ZoneId zoneId = ZoneId.systemDefault();
        System.out.println(zoneId);
        System.out.println("-----------------------");

        // 获取一个指定时区
        ZoneId of = ZoneId.of("Africa/Nairobi");
        System.out.println(of);

        ZonedDateTime zonedDateTime = Instant.now().atZone(of); //获取一个带有时区的时间戳对象
        System.out.println(zonedDateTime);
```

#### **ZoneDataTime** 

带时区的时间对象

```java
            static ZonedDateTime now() : 获取当前时间的ZonedDateTime对象
            static ZonedDateTime ofXxxx(...) : 获取指定时间的ZonedDateTime对象
            ZonedDateTime withXxx(时间) : 修改时间系列的方法
            ZonedDateTime minusXxx(时间) : 减少时间系列的方法
            ZonedDateTime plusXxx(时间)  : 增加时间系列的方法
                
        // 获取当前时间的ZonedDateTime对象
        ZonedDateTime now = ZonedDateTime.now();
        System.out.println(now);
        System.out.println("--------------------------");

        // 获取指定时间的ZonedDateTime对象
        ZonedDateTime of = ZonedDateTime.of
                (2008, 8, 8, 8, 8, 8, 8,
                        ZoneId.systemDefault());
        System.out.println(of);
        System.out.println("--------------------------");

        // 修改时间系列的方法
        System.out.println(now.withYear(2008));
        System.out.println(now.withMonth(8));
        System.out.println(now.withDayOfMonth(8));
        System.out.println("--------------------------");

        // 减少时间系列的方法
        System.out.println(now.minusYears(1));
        System.out.println(now.minusMonths(1));
        System.out.println(now.minusDays(1));
        System.out.println("--------------------------");

        // 增加时间系列的方法
        System.out.println(now.plusYears(1));
        System.out.println(now.plusMonths(1));
        System.out.println(now.plusDays(1));
```

### 工具类

Duration：用于计算两个“时间”间隔（秒，纳秒） 

Period：用于计算两个“日期”间隔（年、月、日）

ChronoUnit：用于计算两个“日期”间隔（年、月、日、时、分、秒）

#### Period

```java
		// 此刻年月日
        LocalDate today = LocalDate.now();
        System.out.println(today);

        // 昨天年月日
        LocalDate otherDate = LocalDate.of(2023, 2, 4);
        System.out.println(otherDate);

        //Period对象表示时间的间隔对象
        Period period = Period.between(today, otherDate);    // 第二个参数减第一个参数

        System.out.println(period.getYears());      // 间隔多少年
        System.out.println(period.getMonths());     // 间隔的月份
        System.out.println(period.getDays());       // 间隔的天数
        System.out.println(period.toTotalMonths()); // 总月份
```

#### Duration

```java
		// 此刻日期时间对象
        LocalDateTime today = LocalDateTime.now();
        System.out.println(today);

        // 昨天的日期时间对象
        LocalDateTime otherDate = LocalDateTime.of(2023, 2, 4, 0, 0, 0);

        System.out.println(otherDate);

        Duration duration = Duration.between(otherDate, today); // 第二个参数减第一个参数

        System.out.println(duration.toDays());                  // 两个时间差的天数
        System.out.println(duration.toHours());                 // 两个时间差的小时数
        System.out.println(duration.toMinutes());               // 两个时间差的分钟数
        System.out.println(duration.toMillis());                // 两个时间差的毫秒数
        System.out.println(duration.toNanos());                 // 两个时间差的纳秒数
```

#### ChronoUnit

```java
// 本地日期时间对象：此刻的
        LocalDateTime today = LocalDateTime.now();
        System.out.println(today);

        // 生日时间
        LocalDateTime birthDate = LocalDateTime.of(2023, 2, 4,0, 0, 0);
        System.out.println(birthDate);

        System.out.println("相差的年数：" + ChronoUnit.YEARS.between(birthDate, today));
        System.out.println("相差的月数：" + ChronoUnit.MONTHS.between(birthDate, today));
        System.out.println("相差的周数：" + ChronoUnit.WEEKS.between(birthDate, today));
        System.out.println("相差的天数：" + ChronoUnit.DAYS.between(birthDate, today));
        System.out.println("相差的时数：" + ChronoUnit.HOURS.between(birthDate, today));
        System.out.println("相差的分数：" + ChronoUnit.MINUTES.between(birthDate, today));
        System.out.println("相差的秒数：" + ChronoUnit.SECONDS.between(birthDate, today));
        System.out.println("相差的毫秒数：" + ChronoUnit.MILLIS.between(birthDate, today));
        System.out.println("相差的微秒数：" + ChronoUnit.MICROS.between(birthDate, today));
        System.out.println("相差的纳秒数：" + ChronoUnit.NANOS.between(birthDate, today));
        System.out.println("相差的半天数：" + ChronoUnit.HALF_DAYS.between(birthDate, today));
        System.out.println("相差的十年数：" + ChronoUnit.DECADES.between(birthDate, today));
        System.out.println("相差的世纪（百年）数：" + ChronoUnit.CENTURIES.between(birthDate, today));
        System.out.println("相差的千年数：" + ChronoUnit.MILLENNIA.between(birthDate, today));
        System.out.println("相差的纪元数：" + ChronoUnit.ERAS.between(birthDate, today));
```

# 异常

指的是程序在编译或执行过程中，出现的非正常的情况 (错误)。注意：语法错误不是异常。

## 异常体系

![image-20241114094942883](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202411140949931.png)

**Error：**严重级别问题

常见的 : 栈内存溢出 (StackOverflowError)  堆内存溢出 (OutOfMemoryError)

**Exception**：

RuntimeException及其子类：运行时异常（编译过了，运行期间可能会出现的问题）

> 数组索引越界异常: ArrayIndexOutOfBoundsException
>
> 空指针异常 : NullPointerException
>
> 数学操作异常：ArithmeticException
>
> 类型转换异常：ClassCastException
>
> 数字转换异常： NumberFormatException

除RuntimeException之外所有的异常：编译时异常（语法错误不算）。主要起到提醒作用。需要在运行之前给出解决方案。

## 异常处理方式

**异常的默认处理流程**：

①虚拟机会在出现异常的代码那里自动的创建一个异常对象：ArithmeticException

②异常会从方法中出现的点这里抛出给调用者，调用者最终抛出给JVM虚拟机

③虚拟机接收到异常对象后，先在控制台直接输出异常信息数据

④终止 Java 程序的运行

⑤后续代码没有机会执行了，因为程序已经噶了

### try...catch...

能够将抛出的异常对象捕获，然后执行异常的处理方案

好处：程序可以继续往下执行

```java
try {
    可能会出现异常的代码
} catch(异常类型1 变量) {
    处理异常的方案
} catch(异常类型2 变量) {
    处理异常的方案
}

					执行流程:
                            1. 执行 try {} 中的代码, 看是否有异常对象产生
                            2. 没有 : catch就不会捕获, 后续代码继续执行
                            3. 有 : catch捕获异常对象, 执行catch {} 中的处理方案, 后续代码继续执行
```

注意: 如果使用多个catch, 最大的异常需要放在最后

### throws

用在方法上，作用是声明，声明这个方法中有可能会出现异常

```java
public void method() throws 异常1，异常2，异常3... {
    
}
```

## 自定义异常

分类：

**1**、自定义编译时异常   

定义一个异常类继承Exception. 重写构造器

**2**、自定义运行时异常

定义一个异常类继承RuntimeException.重写构造器。

## Throwable常用方法

| **方法名**                    | **说明**               |
| ----------------------------- | ---------------------- |
| public String getMessage()    | 获取异常的错误原因     |
| public void printStackTrace() | 展示完整的异常错误信息 |

**注意**：子类重写父类方法时，不能抛出父类没有的异常, 或者比父类更大的异常

# 泛型

**没有泛型的时候，集合如何存储数据？**

结论：

如果我们没有给集合指定类型，默认认为所有的数据类型都是Object类型，此时可以往集合添加任意的数据类型（因为它是所有类型的父类）。

带来一个坏处：我们在获取数据的时候，无法使用他的特有行为（这是多态的弊端：不能访问子类的特有功能）。

此时推出了泛型，可以在添加数据的时候就把类型进行统一。而且我们在获取数据的时候，也省的强转了，非常的方便。

**扩展知识：Java中的泛型是伪泛型。**把运行时期的问题提前到了编译期。运行时是没有泛型的。

![image-20240710090856222](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/typora-user-images/image-20240710090856222.png)

**细节：**

1、泛型中不能写基本数据类型

2、指定泛型的具体类型后，传递数据时，可以传入该类型或者子类类型

![image-20240710091204583](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/typora-user-images/image-20240710091204583.png)

3、如果不写泛型，类型默认是Object

## 泛型类：

使用场景：当一个类中，某个变量的数据类型不确定时，就可以定义带有泛型的类

```java
//格式：
修饰符 class 类名<类型> {
    
}
//举例：
public class ArrayList<E> {
    //创建该类对象时，E就确定类型
}
//此处E可以理解为变量，但是不是用来记录数据的，而是记录数据的类型，可以写成：T（Type）/E（Element）/K（Key）/V（Value）等。
```

## 泛型方法：

方法中形参类型不确定时：

1、非静态方法：使用类名后面定义的泛型 或 自己单独声明

```java
public class ArrayList<E> {

    public boolean add(E e) {

    }
}

public <T> void show(T t) {
    //调用该方法时，T就确定类型
}
```

2、静态方法：在方法上声明自己独立的泛型

```java
public static<T> void printArray(T[] array){
    
}

//此处T可以理解为变量，但是不是用来记录数据的，而是记录数据的类型，可以写成：T（Type）/E（Element）/K（Key）/V（Value）等。
```

## 泛型接口：

```java
public class GenericsDemo4 {
    /*
        泛型接口
                1. 实现类, 实现接口的时候确定到具体的类型
                2. 实现类实现接口, 没有指定具体类型, 就让接口的泛型, 跟着类的泛型去匹配
     */
    public static void main(String[] args) {
        InterBImpl<String> i = new InterBImpl<>();

    }
}


interface Inter<E> {
    void show(E e);
}

class InterAImpl implements Inter<String> {

    @Override
    public void show(String s) {

    }
}

class InterBImpl<E> implements Inter<E>{

    @Override
    public void show(E e) {

    }
}
```

## 泛型通配符：

```java
/*
        泛型通配符

                ? : 任意类型

                ? extends E : 可以传入的是E, 或者是E的子类

                ? super E : 可以传入的是E, 或者是E的父类
*/
```

# 树

## 平衡二叉树

- 在二叉查找树（二叉搜索树）的基础上规定：任意节点子树高度差不能超过1

- 当添加一个节点后，不满足平衡二叉树的条件，那么就触发**旋转**机制

- 左旋：

  确定支点：从添加的节点开始**，**不断的往父节点找不平衡的节点

  - 以不平衡的点作为支点
  - 将根节点的右侧往左拉
  - 原先的右子节点变成新的父节点，并把多余的左子节点出让，给已经降级的根节点当右子节点 

- 右旋：

  确定支点：从添加的节点开始**，**不断的往父节点找不平衡的节点

  - 以不平衡的点作为支点
  - 将根节点的左侧往右拉
  - 原先的左子节点变成新的父节点，并把多余的右子节点出让，给已经降级的根节点当左子节点

- 需要旋转的4种情况：
  - 左左：一次右旋
  - 左右：先局部左旋，再整体右旋
  - 右右：一次左旋
  - 右左：先局部右旋，再整体左旋

## 红黑树

- 是一种特殊的二叉查找树，红黑树的每一个节点上都有存储位表示节点的颜色。

- 每一个节点可以是红或者黑；红黑树不是高度平衡的，它的平衡是通过“红黑规则”进行实现的

- 红黑规则：
  - 每一个节点是红色或者是黑色
  - 根节点必须是黑色
  - 如果一个节点没有子节点或者父节点，则该节点相应的指针属性值为Nil，这些Nil视为叶节点，每个叶节点(Nil)是黑色的
  - 如果某一个节点是红色，那么它的子节点必须是黑色(不能出现两个红色节点相连的情况)
  - 对每一个节点，从该节点到其所有后代叶节点的简单路径上，均包含相同数目的黑色节点

![image-20250415155036769](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504151550831.png)

<img src="https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504151551635.png" alt="image-20250415155132562" style="zoom: 33%;" />

- 添加节点的规则：添加节点默认是红色（效率高）

  ![image-20250415155509143](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504151555250.png)

# 集合

集合是一种容器，用来装数据的，类似于数组。但数组定义完成并启动后，长度就固定了。集合大小可变，开发中用的更多。

**单列集合：**

List接口：ArrayList、LinkedList。存取有序、有索引、可以存储重复的

Set接口：TreeSet、HashSet、LinkedHashSet。存取有序、没有索引、不可以存储重复的

```java
一次添加一个元素

单列集合.add("元素1");
单列集合.add("元素2");
单列集合.add("元素3");

所有的单列集合都实现了Collection接口
```

**双列集合：**

TreeMap、HashMap、LinkedHashMap

```java
一次添加两个元素

双列集合.put("元素1","元素1");
双列集合.put("元素2","元素2");
双列集合.put("元素3","元素3");

所有的双列集合都实现了Map接口
```

## Collection

![image-20241114142632386](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202411141426648.png)

```java
Collection<Student> c = new ArrayList<>();

        c.add(new Student("张三", 23));
        c.add(new Student("李四", 24));
        c.add(new Student("王五", 25));

		c.remove(new Student("张三", 23));
        System.out.println(c.contains(new Student("李四", 24)));
		//其中，remove()、contains() 底层依赖对象的equals方法
		//所以，如果想拿内容作比较，那就得重写Student的equals方法 
		//否则，它就是在拿地址值比较，就是删除失败或判断不包含该元素
```

## 集合通用遍历方式

```java
ArrayList<String> list = new ArrayList<>();
for (int i = 0; i < list.size(); i++) {
     String s = list.get(i);
}
// 这种遍历方式只适合有索引的集合。而List接口下的集合有索引，Set接口下的集合没有索引。
```

**三种方式：**

迭代器、增强for循环、foreach方法

```java
/*
        public Iterator<E> iterator() : 获取遍历集合的迭代器
        public E next() : 从集合中获取一个元素
        public boolean hasNext() : 如果仍有元素可以迭代，则返回 true

        注意: 如果next()方法调用次数多了, 会出现NoSuchElementException
     */
    public static void main(String[] args) {
        Collection<Student> c = new ArrayList<>();

        c.add(new Student("张三", 23));
        c.add(new Student("李四", 24));
        c.add(new Student("王五", 25));


        // 1. 获取迭代器
        Iterator<Student> it = c.iterator();  
        //Iterator是接口，所以这里是多态的形式。 所以 c.iterator() 返回的肯定是Iterator接口的实现类
        //c.iterator() 编译看左边，运行看右边。所以这里看ArrayList中国的iterator方法。发现确实返回了Iterator接口的实现类
        

        // 2. 循环判断, 集合中是否还有元素
        while (it.hasNext()) { // 判断当前指针处是否有元素
            // 3. 调用next方法, 将元素取出，并将指针向后移动一位
            Student stu = it.next();
            System.out.println(stu.getName() + "---" + stu.getAge());
        }

        System.out.println("--------------------");

        // 使用增强for循环遍历集合
		// JDK5之后出现的，其内部原理就是一个Iterator迭代器
        for (Student stu : c) {
            System.out.println(stu);
        }

        System.out.println("--------------------");

        // foreach方法遍历集合
        // 源码是增强for循环  所以它的底层也是迭代器
        c.forEach(stu -> System.out.println(stu));

    }
}
```

## List

List因为支持索引，所以多了很多索引操作的独特api

```java
和索引有关的API :

            public void add(int index, E element) : 在指定的索引位置, 添加元素
            public E remove(int index) : 根据索引删除集合中的元素
            public E set(int index, E element) : 根据索引修改集合中的元素
            public E get(int index) : 返回指定索引处的元素
```

```java
List集合的遍历方式 :

            1. 迭代器遍历
            2. 增强for循环
            3. foreach方法
            4. 普通for循环
            5. ListIterator (List集合特有的迭代器)
                
		List<String> list = new ArrayList<>();
        list.add("abc");
        list.add("bbb");
        list.add("ccc");
        list.add("abc");

        for (int i = 0; i < list.size(); i++) {
            String s = list.get(i);
            System.out.println(s);
        }

        System.out.println("---------------");

        ListIterator<String> it = list.listIterator();

        while(it.hasPrevious()){
            String s = it.previous();
            System.out.println(s);
        }

        System.out.println("---------------");

        while (it.hasNext()) {
            String s = it.next();
            System.out.println(s);
        }
```

```java
并发修改异常 : ConcurrentModificationException

           场景: 使用[迭代器]遍历集合的过程中, 调用了[集合对象]的添加, 删除方法, 就会出现此异常

           解决方案: 迭代器的遍历过程中, 不允许使用集合对象的添加或删除, 那就使用迭代器, 自己的添加或删除方法

                        删除方法 : 普通的迭代器有
                        添加方法 : 普通迭代器没有, 需要使用List集合特有的迭代器
                           
        ListIterator<String> it = list.listIterator();
        while (it.hasNext()) {
            String s = it.next();
            if ("温油".equals(s)) {
                it.add("哈哈");
            }
        }

迭代器遍历集合的过程中, 使用集合的删除方法, 删除倒数第二个元素, 就不会出现错误.
    
    	Iterator<String> it = list.iterator();
        while(it.hasNext()){
            String s = it.next();
            if("def".equals(s)){
                list.remove("def");
            }
        }
```

### ArrayList

- **长度可变原理**

1.如果只是创建了ArrayList集合容器，没有进行添加操作时，底层数组长度默认为0，当添加第一个元素时，底层会创建一个新的长度为10的数组

![image-20241023160428994](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/TyporaPic/image-20241023160428994.png)

2.当容量用完时，扩容原数组 1.5 倍大小的新数组

3.将原数组数据，拷贝到新数组中

4.将新元素添加到新数组

- **常用成员方法**

| 方法名                               | 说明                                   |
| ------------------------------------ | -------------------------------------- |
| public boolean add(E e)              | 将指定的元素添加到此集合的末尾         |
| public void add(int index,E element) | 在此集合中的指定位置插入指定的元素     |
| public E get(int  index)             | 返回指定索引处的元素                   |
| public int  size()                   | 返回集合中的元素的个数                 |
| public E remove(int  index)          | 删除指定索引处的元素，返回被删除的元素 |
| public boolean remove(Object o)      | 删除指定的元素，返回删除是否成功       |
| public E set(int index,E element)    | 修改指定索引处的元素，返回被修改的元素 |

### LinkedList

LinkedList 底层基于**双链表**实现的，查询元素慢，增删首尾元素是非常快的

![image-20241114193533944](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202411141935006.png)

由于LinkedList也属于List接口，因此也有get(index)方法，表面看起来是根据索引获取元素，实际上是？

源码：

![image-20250415144519648](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504151445135.png)

即，如果离头部近，那就从头开始遍历。如果离尾部近，那就从尾部向前遍历。

## Set

### TreeSet

作用 : 对集合中的元素进行排序操作 **(底层红黑树实现)**

特点：排序、去重。

两种排序方式：自然排序，比较器排序

**自然排序**

需要实现Comparable<T>接口，此接口强行对实现它的每个类的对象进行整体排序。

其中，T：可以与此对象进行比较的那些对象的类型。

如果Student类没有实现此接口，那么

```java
        TreeSet<Student> ts = new TreeSet<>();

        ts.add(new Student("王五", 25));
//这里调用add方法就会报错，因为不知道用姓名排序还是年龄。
```

```java
                compareTo 方法的返回值 :

                    0 : 表示相等，不存。只会存添加的第一个元素。
                    1 : 正序排列。
                    -1 : 倒序排列。
                        
public static void main(String[] args) {
        TreeSet<Student> ts = new TreeSet<>();

        // 当调用add方法时，TreeSet添加元素的时候，内部会自动调用compareTo方法，根据这个方法的返回值，决定节点怎么走。
        // 负数： 表示小于要比较的值，往左边走
        // 正数： 表示大于要比较的值，往右边走
        // 0 ： 表示等于要比较的值，不存。但如果是第一个元素，那就作为根节点。
        // 取出的顺序：左、中、右
        ts.add(new Student("王五", 25));
        ts.add(new Student("张三", 23));
        ts.add(new Student("李四", 24));
        ts.add(new Student("赵六", 26));
    }
                        
public class Student implements Comparable<Student>{

    // this.xxx - o.xxx  正序
    // o.xxx - this.xxx  降序
    @Override
    public int compareTo(Student o) {
        // 根据年龄做主要排序条件
        int ageResult = o.age - this.age;
        // 根据姓名做次要排序条件
        int nameResult = ageResult == 0 ? o.name.compareTo(this.name) : ageResult;
        // 判断姓名是否相同
        int result = nameResult == 0 ? 1 : nameResult;
        return result;
    }
}

// 字符串也可以进行比较，因为String这个类也实现了Comparable接口，也有compareTo方法。
```

**比较器排序**

```java
    /*
        如果同时具备比较器和自然排序, 会优先按照比较器的规则, 进行排序操作
     */
    public static void main(String[] args) {
        TreeSet<Student> ts = new TreeSet<>(new Comparator<Student>() {
            // o1：this
            @Override
            public int compare(Student o1, Student o2) {
                int ageResult = o1.getAge() - o2.getAge();
                return ageResult == 0 ? o1.getName().compareTo(o2.getName()) : ageResult;
            }
        });

        ts.add(new Student("赵六", 26));
        ts.add(new Student("李四", 24));
        ts.add(new Student("张三", 23));
        ts.add(new Student("王五", 25));

        System.out.println(ts);
    }
```

Java已经写好类, 大多数都具有自然排序的规则, 这些规则放在源代码中, 我们无法修改。 (String, Integer, Double...)

 String : 默认是字典顺序排序

 Integer : 默认是升序排序

 Double : 默认是升序排序

 如果我们要实现的需求, 排序规则, 跟已经具备的自然排序, 不一样.

 这时候就要使用比较器排序.

### HashSet

底层采用**哈希表**存储数据

特点：去重，保证元素唯一性。 前提：需要同时重写对象中的HashCode方法和equals方法。

> 哈希表：
>
> JDK8版本之前：数组+链表
>
> JDK8版本之后：数组+链表+红黑树

当添加对象的时候, 会先调用对象的hashCode方法计算出一个应该存入的索引位置, 查看该位置上是否存在元素。

不存在：直接存；存在：调用equals方法比较内容。false : 存 true : 不存

**HashSet1.7版本原理理解：**

①创建一个默认长度16的数组，数组名table

②根据元素的哈希值跟数组的长度求余计算出应存入的位置

③判断当前位置是否为null，如果是null直接存入

④如果位置不为null，表示有元素，则调用equals方法比较

⑤如果一样，则不存，如果不一样，则存入数组，

- JDK 7新元素占老元素位置，指向老元素 （头插法）
- JDK 8中新元素挂在老元素下面（尾插法）

![image-20250415165328539](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504151653720.png)

**HashSet8版本之后原理理解：**

①创建 HashSet 集合, 内部会存在一个长度为 16 个大小的数组（底层是调HashMap）

![image-20250415165401829](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504151654934.png)

②调用集合的添加方法, 会拿着对象的hashCode方法计算出应存入的索引位置 ( 哈希值 % 数组长度)

![image-20241122110456282](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202411221105487.png)

对哈希值扰动, 进行二次哈希操作, 可以一定程度的减少链表挂载的数量 

向右移动16位是因为，一长串的二进制后面那么多位大部分都是相同的。如果不进行处理，直接进行取模运算，算出来的hashcode很容易相同。

![image-20250415165651459](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504151656622.png)

得到哈希值后：

![image-20241122110653236](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202411221106344.png)

n就是数组的长度。这种计算方式和 对数组长度取模 是一样的。

③判断索引位置元素是否是null。是 : 存入  不是: 说明有元素, 调用equals方法比较内容

![image-20250415165826732](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504151658018.png)

**如何提高查询性能**？

1.扩容数组

扩容数组的条件 : 

A: 当数组中的元素个数到达了 16 * 0.75 (加载因子) = 12。扩容原数组 2 倍的大小。

B:链表挂载的元素超过了8 (阈值) 个 , 并且数组长度没有超过64。

2.链表转红黑树

链表挂载的元素超过了8 (阈值) 个, 并且数组长度到达了64 。

### LinkedHashSet

特点：有序（保证存取顺序）、不重复、无索引。

原理：底层数据结构是依然**哈希表**，只是每个元素又额外的多了一个双链表的机制记录存储的顺序。

![image-20241122144155762](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202411221441055.png)

## Collections

java.utils.Collections:集合工具类

Collections并不属于集合，是用来操作集合的工具类。

### 可变参数

可变参数用在形参中可以接收多个数据。

可变参数的格式：数据类型...参数名称

传输参数非常灵活，方便，可以不传输参数，可以传输1个或者多个，也可以传输一个数组

可变参数在方法内部本质上就是一个**数组**。

**注意事项**：

1.一个形参列表中可变参数只能有一个

2.可变参数必须放在形参列表的最后面

**常用方法**：

| 方法名称                                                     | 说明                                   |
| ------------------------------------------------------------ | -------------------------------------- |
| public static  <T> boolean addAll(Collection<? super T> c, T... elements) | 给集合对象批量添加元素                 |
| public static  void shuffle(List<?> list)                    | 打乱List集合元素的顺序                 |
| public static <T> int binarySearch  (List<T> list, T key)    | 以二分查找法查找元素  （排好序的数据） |
| public static <T> void  max/min(Collection<T> coll)          | 根据默认的自然排序获取最大/小值        |
| public static <T> void swap(List<?> list,  int i, int j)     | 交换集合中指定位置的元素               |

**Collections排序相关API：**

适用范围：只能对List集合的排序

排序方式1：

| 方法名称                                  | 说明                         |
| ----------------------------------------- | ---------------------------- |
| public static <T> void sort(List<T> list) | 将集合中元素按照默认规则排序 |

注意：本方式不可以直接对自定义类型的List集合排序，除非自定义类型实现了比较规则Comparable接口

排序方式2：

| 方法名称                                                     | 说明                         |
| ------------------------------------------------------------ | ---------------------------- |
| public static <T> void sort(List<T>  list，Comparator<? super T> c) | 将集合中元素按照指定规则排序 |

## Map

Map 集合是一种双列集合，每个元素包含两个数据

Map 集合的每个元素的格式：key = value(键值对元素)

- key (键) : 不允许重复

- value (值) : 允许重复

键和值是一一对应的，每个键只能找到自己对应的值

key + value 这个整体 我们称之为“键值对”或者“键值对对象” 在Java中使用Entry对象表示

**常见API：**

Map是双列集合的顶层接口，它的功能是全部双列集合都可以继承使用的

| 方法名称                            | 说明                                                         |
| ----------------------------------- | ------------------------------------------------------------ |
| V  put(K key,V value)               | 添加元素  (修改:如果键已经存在了，就会使用新值，替换旧值)。返回被覆盖掉的旧值 |
| V  remove(Object key)               | 根据键删除键值对元素 。返回被删除的键所对应的值              |
| void  clear()                       | 移除所有的键值对元素                                         |
| boolean containsKey(Object key)     | 判断集合是否包含指定的键                                     |
| boolean containsValue(Object value) | 判断集合是否包含指定的值                                     |
| boolean isEmpty()                   | 判断集合是否为空                                             |
| int  size()                         | 集合的长度，也就是集合中键值对的个数                         |

```java
        双列集合底层的数据结构, 都是针对于键有效, 跟值没有关系.

            HashMap : 哈希表。键唯一 (重写hashCode和equals方法)
            TreeMap : 红黑树。键排序 (实现Comparable接口, 重写compareTo方法)
            LinkedHashMap : 哈希表+双向链。键唯一, 且可以保证存取顺序
```

### Map集合的三种遍历方式

1.通过键找值

| 方法名称          | 说明                    |
| ----------------- | ----------------------- |
| V get(Object key) | 根据键查找对应的值      |
| Set<K> keySet()   | 获取 Map 集合中所有的键 |

- 调用 keySet 方法获取所有的键 (得到的是Set集合)

- 遍历 Set 集合, 获取每一个键

- 遍历的过程中调用 get 方法, 根据键找值

2.通过键值对对象获取键和值

| 方法名称                       | 说明                       |
| ------------------------------ | -------------------------- |
| Set<Map.Entry<K,V>> entrySet() | 获取集合中所有的键值对对象 |

| **方法名** | **说明** |
| ---------- | -------- |
| getKey()   | 获取键   |
| getValue() | 获取值   |

- 调用 entrySet 方法获取所有的键值对对象(得到的是Set集合)

- 遍历 Set 集合, 获取每一个键值对对象

- 通过键值对对象的 getKey() getValue() 获取键和值

3.foreach方法遍历

| 方法名称                                                     | 说明                    |
| ------------------------------------------------------------ | ----------------------- |
| default void forEach  (BiConsumer<? super K,? super V> action) | 遍历Map集合, 获取键和值 |

```java
        HashMap<String, String> hm = new HashMap<>();
        hm.put("张三", "北京");
        hm.put("李四", "上海");
        hm.put("王五", "成都");

        hm.forEach((key, value) -> System.out.println(key + "---" + value));

        hm.forEach((k,v) -> System.out.println(k + "---" + v));
```

# Stream流

配合Lambda表达式，简化集合和数组操作

```java
    /*
        获取Stream流对象：
            - 将数据放在流水线的传送带上

            1. 集合获取 Stream 流对象 (使用Collection接口中的默认方法)
                    default Stream<E> stream()

                    * Map集合获取Stream流对象, 需要间接获取
                            - map.entrySet().stream()

            2. 数组获取 Stream 流对象 (使用Arrays数组工具类中的静态方法)
                    static <T> Stream<T> stream (T[] array)

            3. 零散的数据获取 Stream 流对象 (使用 Stream 类中的静态方法)
                    static <T> Stream<T> of(T... values)
     */

		list.stream().forEach(s -> System.out.println(s));
        set.stream().forEach(s -> System.out.println(s));
		Set<Map.Entry<String, Integer>> entrySet = map.entrySet();
        entrySet.stream().forEach(s -> System.out.println(s));

		int[] arr1 = {11, 22, 33};
		Arrays.stream(arr1).forEach(s -> System.out.println(s));

		Stream.of(1, 2, 3, 4, 5, 6).forEach(s -> System.out.println(s));

```

```java
    /*
        Stream流的中间操作方法
            - 操作后返回Stream对象, 可以继续操作

            Stream<T> filter(Predicate<? super T> predicate) 用于对流中的数据进行过滤
            Stream<T> limit(long maxSize) 获取前几个元素
            Stream<T> skip(long n) 跳过前几个元素
            Stream<T> distinct() 去除流中重复的元素依赖 (hashCode 和 equals方法)
            static <T> Stream<T> concat(Stream a, Stream b) 合并a和b两个流为一个流

            注意事项: 流对象已经被消费过(使用过), 就不允许再次消费了.

     */

		// 需求: 将集合中以 【张】 开头的数据，过滤出来并打印在控制台
        ArrayList<String> list = new ArrayList<String>();
        list.add("林青霞");
        list.add("张曼玉");
        list.add("王祖贤");
        list.add("柳岩");
        list.add("张敏");
        list.add("张无忌");
		list.stream().filter(s -> s.startsWith("张")).filter(s -> s.length() == 3).forEach(s -> System.out.println(s));

		// 需求1: 取前3个数据在控制台输出
        list.stream().limit(3).forEach(s -> System.out.println(s));
		// 需求2: 跳过3个元素, 把剩下的元素在控制台输出
        list.stream().skip(3).forEach(s -> System.out.println(s));
		// 需求3: 跳过2个元素, 把剩下的元素中前2个在控制台输出
        list.stream().skip(2).limit(2).forEach(s -> System.out.println(s));
		// 需求4: 取前4个数据组成一个流
        Stream<String> s1 = list.stream().limit(4);
		// 需求5: 跳过2个数据组成一个流
        Stream<String> s2 = list.stream().skip(2);
		// 需求6: 合并需求4和需求5得到的流
        Stream<String> s3 = Stream.concat(s1, s2);

		//注意事项：如果流对象已经被消费过，就不允许再次使用了。也就是说如果下面再使用s3，就会报错。


		// 需求7: 合并需求4和需求5得到的流, 并把结果在控制台输出，要求字符串元素不能重复
        s3.distinct().forEach(s -> System.out.println(s));
```

```java
    /*
        Stream流的终结操作方法
            - 流水线中的最后一道工序

            public void forEach (Consumer action) 对此流的每个元素执行遍历操作
            public long count () 返回此流中的元素数
     */

		long count = Stream.of(1, 2, 3, 4, 5, 6).filter(s -> s % 2 == 0).count();
```

```java
    /*
        Stream流的收集操作

            public R collect (Collector c) : 将流中的数据收集到集合

                Collectors
                    public static <T> Collector toList()
                    public static <T> Collector toSet()
                    public static  Collector toMap(Function keyMapper , Function valueMapper)
     */

List<Integer> list1 = Stream.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10).filter(s -> s % 2 == 0).collect(Collectors.toList());
Set<Integer> list2 = Stream.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 10, 10).filter(s -> s % 2 == 0).collect(Collectors.toSet());

	/*
        创建一个 ArrayList 集合，并添加以下字符串
        "张三,23"
        "李四,24"
        "王五,25"
        保留年龄大于等于24岁的人，并将结果收集到Map集合中，姓名为键，年龄为值
     */
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<>();
        list.add("张三,23");
        list.add("李四,24");
        list.add("王五,25");

        Map<String, Integer> map = list.stream().filter(new Predicate<String>() {
            @Override
            public boolean test(String s) {
                return Integer.parseInt(s.split(",")[1]) >= 24;
            }
        }).collect(Collectors.toMap(new Function<String, String>() {
            @Override
            public String apply(String s) {
                return s.split(",")[0];
            }
        }, new Function<String, Integer>() {
            @Override
            public Integer apply(String s) {
                return Integer.parseInt(s.split(",")[1]);
            }
        }));

        System.out.println(map);
    }
```

# File类

```java
    /*
        File类介绍 : 文件或文件夹对象

        构造方法 :
            1. public File (String pathname) : 根据传入的字符串路径封装File对象。这个路径可以是存在的，也可以是不存在的
            2. public File (String parent, String child) : 根据传入的父级路径和子级路径来封装File对象
            3. public File (File  parent, String child) : 根据传入的父级路径(File类型)和子级路径来封装File对象

     */
    public static void main(String[] args) throws IOException {
        File f1 = new File("D:\\A.txt");
        f1.createNewFile();

        File f2 = new File("D:\\test");
        System.out.println(f2.exists());

        File f3 = new File("D:\\", "test");
        System.out.println(f3.exists());

        File f4 = new File(new File("D:\\"), "test");
        System.out.println(f4.exists());
    }
```

```java
/*
        File类常见方法 :

            1. 判断相关
                    public boolean isDirectory() : 判断是否是文件夹
                    public boolean isFile() : 判断是否是文件
                    public boolean exists() : 判断是否存在

            2. 获取相关
                    public long length() : 返回文件的大小(字节数量)
                                                * 文件对象操作, 返回正确的字节个数
                                                * 文件夹对象操作, 返回的是错误的字节个数

                    public String getAbsolutePath() : 返回文件的绝对路径
                    public String getPath() : 返回定义文件时使用的路径
                    public String getName() : 返回文件的名称，带后缀
                    public long lastModified() : 返回文件的最后修改时间（时间毫秒值）

     */
    public static void main(String[] args) {
        File f1 = new File("D:\\A.txt");
        System.out.println(f1.isDirectory());       // false
        System.out.println(f1.isFile());            // true
        System.out.println(f1.exists());            // true

        System.out.println("----------------------");

        File f2 = new File("D:\\test");

        System.out.println(f1.length());
        System.out.println(f2.length());

        System.out.println("----------------------");

        File f3 = new File("A.txt");
        System.out.println(f3.getAbsolutePath());

        System.out.println("----------------------");

        System.out.println(f1.getName());
        System.out.println(f2.getName());
        System.out.println(f3.getName());

        System.out.println("----------------------");

        long time = f1.lastModified();
        System.out.println(new Date(time));
    }
```

```java
    /*
        File类的创建方法和删除方法 :

            public boolean createNewFile() :创建文件
            public boolean mkdir() : 创建单级文件夹
            public boolean mkdirs() : 创建多级文件夹
            public boolean delete() : 删除文件或文件夹
                            - delete 方法删除文件夹, 只能删除空的文件夹.
     */
    public static void main(String[] args) throws IOException {
        File f1 = new File("src\\com\\itheima\\day12\\B.txt");
        System.out.println(f1.createNewFile());

        File f2 = new File("src\\com\\itheima\\day12\\aaa");
        System.out.println(f2.mkdirs());

        File f3 = new File("src\\com\\itheima\\day12\\C.txt");
        System.out.println(f3.mkdirs());

        System.out.println(f1.delete());
        System.out.println(f2.delete());
    }
```

```java
    /*
        File类的遍历方法 :

            public File[] listFiles() 获取当前目录下所有的  "一级文件对象"  返回 File 数组

     */
    public static void main(String[] args) {
        File f = new File("D:\\test");

        File[] files = f.listFiles();

        for (File file : files) {
            System.out.println(file);
        }
    }
```

# IO流

## 体系结构

![image-20241126165841479](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202411261658773.png)

## FileOutputStream字节输出流

```java
    /*
        字节流写出数据

            构造方法:
                    1. public FileOutputStream(String name) : 输出流关联文件, 文件路径以字符串形式给出
                    2. public FileOutputStream(String name, boolean append) : 第二个参数是追加写入的开关
                    3. public FileOutputStream(File file) : 输出流关联文件, 文件路径以File对象形式给出
                    4. public FileOutputStream(File file, boolean append) : 第二个参数是追加写入的开关

            成员方法:
                    public void write(int i) : 写出一个字节
                    public void write(byte[] bys) : 写出一个字节数组
                    public void write(byte[] bys, int off, int len) : 写出字节数组的一部分

            细节:
                    输出流关联文件, 文件如果不存在: 会自动创建出来
                                    如果文件存在: 会清空现有的内容, 然后再进行写入操作
     */
    public static void main(String[] args) throws IOException {
        // 创建字节输出流对象, 关联文件
        FileOutputStream fos = new FileOutputStream("D:\\A.txt", true);  //true:以追加形式写入文件中

        byte[] bys = {97, 98, 99};

        // 写出数据
        fos.write(97); //a
        fos.write(98); //b
        fos.write(99); //c
        fos.write(bys);
        fos.write("你好你好".getBytes());
        fos.write(bys, 1, 2);
    }
```

```java
    /*
        流对象使用完毕后需要调用close方法关闭
     */
        fos.close();
```

**标准关流流程**

JDK7版本之前：

```java
    /*
        IO流的异常处理方式: jdk7版本之前 
     */
    public static void main(String[] args) {

        FileOutputStream fos = null;

        try {
            fos = new FileOutputStream("D:\\B.txt");
            fos.write("abc".getBytes());
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if(fos != null){
                try {
                    fos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }

    }
```

JDK7版本之后：

```java
    /*
        IO流的异常处理方式: jdk7版本开始
     */
		// try()中的对象需要实现过AutoCloseable接口
		// 实现AutoCloseable接口的类的对象会自动调用close方法
        try(FileOutputStream fos = new FileOutputStream("D:\\B.txt");) { 
            fos.write("abc".getBytes());
        } catch (IOException e) {
            e.printStackTrace();
        }
```

## FileInputStream字节输入流

| 构造方法                     | 说明                                       |
| ---------------------------- | ------------------------------------------ |
| FileInputStream(String name) | 输入流关联文件, 文件路径以字符串形式给出   |
| FileInputStream(File  file)  | 输入流关联文件, 文件路径以File对象形式给出 |

关联的文件不存在会抛出 FileNotFoundException 异常，文件夹的话会拒绝访问。

| 成员方法            | 说明                                                         |
| ------------------- | ------------------------------------------------------------ |
| int  read()         | 读取一个字节并返回, 如果到达文件结尾则返回  -1               |
| int  read(byte[] b) | 将读取到字节, 放到传入的数组  返回读取到的有效字节个数  如果到达文件结尾则返回  -1 |

![image-20241127100225881](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202411271012422.png)

String类中的一个构造方法：

| 构造方法                                                | 说明                                                         |
| ------------------------------------------------------- | ------------------------------------------------------------ |
| public  String  (byte[]  bytes, int offset, int length) | 将字节数组转换为字符串  参数1 :  字节数组  参数2 : 起始索引  参数3 : 转换的个数 |

```java
        FileInputStream fis = new FileInputStream("D:\\A.txt");

        // 准备菜篮子, 用于装(字节)
        byte[] bys = new byte[2];

        int len;
        while( (len = fis.read(bys)) != -1 ){
            String s = new String(bys, 0, len);
            System.out.print(s); 
        }

        fis.close();
```

## 字节缓冲流

字节缓冲流在源代码中内置了字节数组，可以提高读写效率

| 构造方法                                | 说明                       |
| --------------------------------------- | -------------------------- |
| BufferedInputStream(InputStream in)     | 对传入的字节输入流进行包装 |
| BufferedOutputStream(OutputStream  out) | 对传入的字节输出流进行包装 |

注意: 缓冲流不具备读写功能, 它们只是对普通的流对象进行包装。真正和文件建立关联的, 还是普通的流对象

![image-20241127101847572](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202411271018833.png)

![image-20241127104558451](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202411271045573.png)

![image-20241127104606338](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202411271046604.png)

## FileReader字符输入流

用于读取纯文本文件，解决中文乱码问题

| 构造方法                    | 说明                                       |
| --------------------------- | ------------------------------------------ |
| FileReader(String fileName) | 字符输入流关联文件，路径以字符串形式给出   |
| FileReader(File  file)      | 字符输入流关联文件，路径以File对象形式给出 |

| 成员方法                      | 说明                                       |
| ----------------------------- | ------------------------------------------ |
| public  int read()            | 读取单个字符                               |
| public  int read(char[] cbuf) | 读取一个字符数组, 返回读取到的有效字符个数 |

编码：字符转字节

| 成员方法                                     | 说明                                   |
| -------------------------------------------- | -------------------------------------- |
| public  byte[] getBytes()                    | 使用平台默认字符编码方式, 对字符串编码 |
| public  byte[] getBytes(String  charsetName) | 使用使用字符编码方式, 对字符串编码     |

解码: 字节转字符

| 构造方法                                         | 说明                                   |
| ------------------------------------------------ | -------------------------------------- |
| public  String(byte[] bytes)                     | 使用平台默认字符编码方式, 对字符串解码 |
| public  String(byte[] bytes, String charsetName) | 使用使用字符编码方式, 对字符串解码     |

```
重点记忆: 中文字符, 通常都是由负数的字节进行组成的.
            特殊情况: 可能会出现正数, 但是就算有正数, 第一个字节肯定是负数

注意事项: 今后如果出现乱码问题, 大概率是因为编解码方式不一致所导致的.
```

## FileWriter字符输出流

| 构造方法                                      | 说明                                       |
| --------------------------------------------- | ------------------------------------------ |
| FileWriter(String fileName)                   | 字符输出流关联文件，路径以字符串形式给出   |
| FileWriter(String  fileName, boolean  append) | 参数2: 追加写入的开关                      |
| FileWriter(File  file)                        | 字符输出流关联文件，路径以File对象形式给出 |
| FileWriter(File  file, boolean  append)       | 参数2: 追加写入的开关                      |

| 成员方法                                         | 说明                 |
| ------------------------------------------------ | -------------------- |
| public  void write(int c)                        | 写出单个字符         |
| public  void write(char[] cbuf)                  | 写出一个字符数组     |
| public  write(char[] cbuf,  int off, int len)    | 写出字符数组的一部分 |
| public  void write(String str)                   | 写出字符串           |
| public  void write(String str, int off, int len) | 写出字符串的一部分   |

字符输出流写出数据，需要调用flush或close方法，数据才会写出。Flush后可以继续写出。Close 后不能继续写出

```
flush() : 刷出数据, 刷出后可以继续写出
close() : 关闭流释放资源, 顺便刷出数据, 关闭后不可以继续写出
```

## 字符缓冲流

| 构造方法                       | 说明                       |
| ------------------------------ | -------------------------- |
| BufferedReader(Reader  reader) | 对传入的字符输入流进行包装 |
| BufferedWriter(Writer  writer) | 对传入的字符输出流进行包装 |

BufferedReader：

| 成员方法                  | 说明                                 |
| ------------------------- | ------------------------------------ |
| public  String readLine() | 读取一行字符串, 读取到末尾返回  null |

BufferedWriter：

| 成员方法               | 说明                       |
| ---------------------- | -------------------------- |
| public  void newLine() | 写出换行符  (具有跨平台性) |

## 转换流

1.按照指定的字符编码读写操作

2.将字节流转换为字符流进行操作

```java
    /*
        转换流按照指定的字符编码读写

        构造方法:
            InputStreamReader(InputStream in, String CharsetName)  按照指定的字符编码读取
            OutputStreamWriter(OutputStream in, String CharsetName)  按照指定的字符编码写出
     */
```

## 序列化流

可以在流中，以字节的形式直接读写对象

```
序列化流读写对象
    序列化: 将对象写出到文件
                public ObjectOutputStream(OutputStream out)
    反序列化: 从文件中将对象读取到程序
                public ObjectInputStream(InputStream in)
```

| 成员方法             | 说明                     |
| -------------------- | ------------------------ |
| Object  readObject() | 从流中读取对象(反序列化) |

| 成员方法                      | 说明                       |
| ----------------------------- | -------------------------- |
| void  writeObject(Object obj) | 在流中将对象写出  (序列化) |

使用readObjec和writeObject的前提是，对象要实现了**Serializable**接口。

transient 关键字的作用：被transient修饰的成员变量不会被序列化

## 打印流

打印流可以实现方便、高效的打印数据到文件中去，并且可以指定字符编码。可以实现打印什么数据就是什么数据，例如打印整数 97 写出去就是 97

**PrintStream**

| 构造器                                           | 说明                                 |
| ------------------------------------------------ | ------------------------------------ |
| public PrintStream (OutputStream os)             | 打印流直接通向字节输出流管道         |
| public PrintStream (File f)                      | 打印流直接通向文件对象               |
| public PrintStream (String filepath)             | 打印流直接通向文件路径               |
| public PrintStream (File f, String csn)          | 打印流直接通向文件对象, 指定字符编码 |
| public PrintStream (String filepath, String csn) | 打印流直接通向文件路径, 指定字符编码 |

| 方法                               | 说明                   |
| ---------------------------------- | ---------------------- |
| public void print\println(Xxx  xx) | 打印任意类型的数据出去 |

**PrintWriter字符打印流**

| 构造器                                           | 说明                                   |
| ------------------------------------------------ | -------------------------------------- |
| public PrintWriter (OutputStream os)             | 打印流直接通向字节输出流管道           |
| public PrintWriter (Writer w)                    | 打印流直接通向字符输出流管道           |
| public PrintWriter (File f)                      | 打印流直接通向文件对象                 |
| public PrintWriter (String filepath)             | 打印流直接通向文件路径                 |
| public PrintWriter (File f, String csn)          | 打印流直接通向文件对象  , 指定字符编码 |
| public PrintWriter (String filepath, String csn) | 打印流直接通向文件路径  , 指定字符编码 |

| 方法                               | 说明                   |
| ---------------------------------- | ---------------------- |
| public void print\println(Xxx  xx) | 打印任意类型的数据出去 |

## Properties集合

其实就是一个Map集合。内部存在着两个方法，可以很方便的将集合中的键值对写入文件，也可以方便的从文件中读取 常用于加载配置文件

![image-20241128134046099](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202411281340237.png)

| 方法                                          | 说明                 |
| --------------------------------------------- | -------------------- |
| Object setProperty(String  key, String value) | 添加(修改)一个键值对 |
| String getProperty(String  key)               | 根据键获取值         |
| Set<String> stringPropertyNames()             | 获取集合中所有的键   |

```java
    /*
        Properties作为集合的使用

            Object setProperty (String key, String value) : 类似Map集合的put方法
            String getProperty (String key) : 类似Map集合的get方法
            Set<String> stringPropertyNames() : 类似Map是集合的keySet方法
     */
    public static void main(String[] args) {
        Properties prop = new Properties();
        prop.setProperty("username", "admin");
        prop.setProperty("password", "1234");   

        Set<String> keySet = prop.stringPropertyNames();

        for (String key : keySet) {
            System.out.println(key + "---" + prop.getProperty(key));
        }
    }
```

**Properties和IO有关的方法**

| 方法                                           | 说明                             |
| ---------------------------------------------- | -------------------------------- |
| void load(InputStream inStream)                | 从流中加载数据到集合(字节流)     |
| void  load(Reader reader)                      | 从流中加载数据到集合(字符流)     |
| void store(OutputStream out,  String comments) | 将集合的键值对写出到文件(字节流) |
| void  store(Writer writer, String  comments)   | 将集合的键值对写出到文件(字符流) |

```java
    /*
        Properties 和 IO 有关的方法

            void load(InputStream inStream)     从输入字节流读取属性列表（键和元素对）
            void load(Reader reader)            从输入字符流读取属性列表（键和元素对）
            void store(OutputStream out, String comments)   将集合的键值对写出到文件(字节流)
            void store(Writer writer, String comments)      将集合的键值对写出到文件(字符流)
     */
    public static void main(String[] args) throws IOException {

        Properties prop = new Properties();

        FileInputStream fis = new FileInputStream("day14-code\\config.properties");

        prop.load(fis);

        fis.close();

        System.out.println(prop);

    }

    private static void method() throws IOException {
        Properties prop = new Properties();
        prop.setProperty("username", "admin");
        prop.setProperty("password", "1234");

        FileWriter fos = new FileWriter("day14-code\\config.properties");

        prop.store(fos, null);

        fos.close();
    }
```

![image-20241128152157170](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202411281521438.png)



# 进程和线程

## 进程介绍

进程 (Process) 是计算机中的程序关于某数据集合上的一次运行活动，是系统进行资源分配的基本单位

1.独立性：每一个进程都有自己的空间，在没有经过进程本身允许的情况下，一个进程不可以直接访问其它的的进程空间

2.动态性：进程是动态产生，动态消亡的

3.并发性：任何进程都可以同其它进程一起并发执行

> 并行：在同一时刻，有多个指令在多个CPU上【同时】执行 
>
> 并发：在同一时刻，有多个指令在单个CPU上【交替】执行

多进程同时工作，对于一个 CPU 而言，它是在多个进程间轮换执行的。

## 线程介绍

线程 (Thread) : 进程可以同时执行多个任务，每个任务就是线程

一个线程，在一个时刻，只能运行在一个处理器核心上

![image-20241128154100082](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202411281541210.png)

多线程的意义：提高执行效率，同时处理多个任务。

## java开启线程的方式

![image-20241128154511180](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202411281545497.png)

```java
    /*
        开启线程第一种方式: 继承Thread类

        1. 编写一个类继承Thread
        2. 重写run方法
        3. 将线程任务代码写在run方法中
        4. 创建线程对象
        5. 调用start方法开启线程

        细节: 调用start方法开启线程, 会自动的调用run方法执行.

        注意: 只有调用了start方法, 才是开启了新的线程，而不能调用run方法，否则只是调用了方法，而不是开启新线程
     */
    public static void main(String[] args) {
        // 4. 创建线程对象
        MyThread mt1 = new MyThread();
        MyThread mt2 = new MyThread();
        // 5. 调用start方法开启线程
        mt1.start();
        mt2.start();
    }
}

// 1. 编写一个类继承Thread
class MyThread extends Thread {
    // 2. 重写run方法
    @Override
    public void run() {
        // 3. 将线程任务代码写在run方法中
        for (int i = 1; i <= 200; i++) {
            System.out.println("线程任务执行了" + i);
        }
    }
}
```

```java
    /*
        Java程序默认是多线程的, 程序启动后默认会存在两条线程
            1. 主线程
            2. 垃圾回收线程。当一个类的对象被判定为内存中的垃圾，并且被垃圾回收器清理了，那么就会执行finalize方法。
     */
    public static void main(String[] args) {

        for (int i = 1; i <= 500000; i++){
            new Demo();
        }

        for (int i = 1; i <= 2000; i++) {
            System.out.println("main线程执行了");
        }

        MyThread mt = new MyThread();
        mt.start();
    }
}

class Demo {
    @Override
    protected void finalize() throws Throwable {
        System.out.println("垃圾被清理了");
    }
}
```

```java
    /*
        开启线程的第二种方式: 实现Runnable接口

        1. 编写一个类实现Runnable接口
        2. 重写run方法
        3. 将线程任务代码写在run方法中
        4. 创建线程任务资源
        5. 创建线程对象, 将资源传入
        6. 使用线程对象调用start方法, 开启线程
     */
    public static void main(String[] args) {
        // 4. 创建线程任务资源
        MyRunnable mr = new MyRunnable();
        // 5. 创建线程对象, 将资源传入
        Thread t = new Thread(mr);
        // 6. 使用线程对象调用start方法, 开启线程
        t.start();

        for (int i = 1; i <= 2000; i++) {
            System.out.println("main线程执行了");
        }
    }
}

// 1. 编写一个类实现Runnable接口
class MyRunnable implements Runnable {
    // 2. 重写run方法
    @Override
    public void run() {
        // 3. 将线程任务代码写在run方法中
        for (int i = 1; i <= 200; i++) {
            System.out.println("线程任务执行了" + i);
        }
    }
}
```

```java
    /*
        开启线程的第三种方式: 实现Callable接口

        1. 编写一个类实现Callable接口
        2. 重写call方法
        3. 将线程任务代码写在call方法中
        4. 创建线程任务资源对象
        5. 创建线程任务对象, 封装线程资源
        6. 创建线程对象, 传入线程任务
        7. 使用线程对象调用start开启线程
     */
    public static void main(String[] args) throws Exception {
        // 创建线程任务资源对象
        MyCallable mc = new MyCallable();
        // 创建线程任务对象, 封装线程资源
        FutureTask<Integer> task1 = new FutureTask<>(mc);
        FutureTask<Integer> task2 = new FutureTask<>(mc);
        // 创建线程对象, 传入线程任务
        Thread t1 = new Thread(task1);
        Thread t2 = new Thread(task2);
        // 使用线程对象调用start开启线程
        t1.start();
        t2.start();

        Integer result1 = task1.get();
        Integer result2 = task2.get();
        System.out.println("task1获取到的结果为:" + result1);
        System.out.println("task2获取到的结果为:" + result2);

    }
}

// 1. 编写一个类实现Callable接口
class MyCallable implements Callable<Integer> {
    // 2. 重写call方法
    @Override
    public Integer call() throws Exception {
        // 3. 将线程任务代码写在call方法中
        int sum = 0;
        for (int i = 1; i <= 100; i++) {
            sum += i;
            System.out.println("sum=" + sum);
        }
        return sum;
    }
```

## 线程相关方法

| 方法名称                          | 说明                                                   |
| --------------------------------- | ------------------------------------------------------ |
| String getName()                  | 返回此线程的名称                                       |
| void setName(String  name)        | 设置线程的名字（构造方法也可以设置名字）               |
| static  Thread currentThread()    | 获取当前线程的对象                                     |
| static void  sleep(long time)     | 让线程休眠指定的时间，单位为毫秒                       |
| setPriority(int newPriority)      | 设置线程的优先级                                       |
| final  int getPriority()          | 获取线程的优先级                                       |
| final  void setDaemon(boolean on) | 设置为守护线程。当一个线程结束后，守护线程也一并结束。 |

## 线程安全和同步

安全问题出现的条件：是多线程环境；有共享数据；有多条语句操作共享数据

同步技术: 将多条语句操作共享数据的代码给锁起来，让任意时刻只能有一个线程可以执行

### **同步代码块**

```java
synchronized(锁对象) {
    多条语句操作共享数据的代码
}
锁对象可以是任意对象，但是需要保证多条线程的锁对象，是同一把锁
同步可以解决多线程的数据安全问题，但是也会降低程序的运行效率
```

```java
public static void main(String[] args) {
        TicketTask2 t1 = new TicketTask2();
        TicketTask2 t2 = new TicketTask2();
        TicketTask2 t3 = new TicketTask2();

        t1.setName("窗口1");
        t2.setName("窗口2");
        t3.setName("窗口3");

        t1.start();
        t2.start();
        t3.start();
    }
}

class TicketTask2 extends Thread {

    //这两个变量前面必须得加static，才能保证t1，t2，t3所用的tickets和o是同一个。否则t1，t2，t3创建对象时候会各自new。
    //被static所修饰的成员内存中只有一份
    private static int tickets = 2000;
    private static Object o = new Object();
    

    @Override
    public void run() {
        while (true) {
            synchronized (o) {
                if (tickets == 0) {
                    break;
                }

                System.out.println(getName() + "卖出了第" + tickets + "号票");
                tickets--;
            }
        }
    }
}

class TicketTask3 extends Thread {

    private static int tickets = 2000;

    @Override
    public void run() {
        while (true) {
            synchronized (TicketTask3.class) { //使用类的字节码文件作为锁。一个类加载字节码时只加载一次，所以它是唯一的
                if (tickets == 0) {
                    break;
                }

                System.out.println(getName() + "卖出了第" + tickets + "号票");
                tickets--;
            }
        }
    }
}
```

```java
    public static void main(String[] args) {
        TicketTask task = new TicketTask();

        Thread t1 = new Thread(task, "窗口1");
        Thread t2 = new Thread(task, "窗口2");
        Thread t3 = new Thread(task, "窗口3");

        t1.start();
        t2.start();
        t3.start();

    }
}

class TicketTask implements Runnable {

    private Object o = new Object(); //这里不用加static，因为这种方法task只new了一次。

    private int tickets = 2000;

    @Override
    public void run() {
        while (true) {
            synchronized (o) {
                if (tickets == 0) {
                    break;
                }

                System.out.println(Thread.currentThread().getName() + "卖出了第" + tickets + "号票");
                tickets--;
            }
        }
    }
}
```

### 同步方法

```java
在方法的返回值类型前面加入 synchronized 关键字 
public synchronized void method() {
}
方法分为静态和非静态
静态方法的内部锁对象是字节码对象，非静态方法的内部锁对象是 this
```

```java
    public static void main(String[] args) {
        TicketTask4 task = new TicketTask4();

        Thread t1 = new Thread(task, "窗口1");
        Thread t2 = new Thread(task, "窗口2");

        t1.start();
        t2.start();
    }
}

class TicketTask4 implements Runnable {

    private int tickets = 100;

    @Override
    public void run() {
        while (true) {
                if (method()) {
                    break;
                }
    	}

    private synchronized boolean method() {
        if (tickets == 0) {
            return true;
        }

        System.out.println(Thread.currentThread().getName() + "卖出了第" + tickets + "号票");
        tickets--;
        return false;
    }
}
```

### Lock锁

使用 Lock 锁，我们可以更清晰的看到哪里加了锁，哪里释放了锁

Lock 是接口，无法直接创建对象

| 构造方法                | 说明                                    |
| ----------------------- | --------------------------------------- |
| public  ReentrantLock() | 创建一个  ReentrantLock  的实例  互斥锁 |

| 成员方法       | 说明   |
| -------------- | ------ |
| void  lock()   | 加锁   |
| void unlock(); | 释放锁 |

```java
    /*
        互斥锁 ReentrantLock()
     */
    public static void main(String[] args) {
        TicketTask6 task = new TicketTask6();

        Thread t1 = new Thread(task, "窗口1");
        Thread t2 = new Thread(task, "窗口2");

        t1.start();
        t2.start();
    }
}

class TicketTask6 implements Runnable {

    private int tickets = 100;
    private ReentrantLock lock = new ReentrantLock();

    @Override
    public void run() {
        while (true) {
            try {
                lock.lock();
                if (tickets == 0) {
                    break;
                }

                System.out.println(Thread.currentThread().getName() + "卖出了第" + tickets + "号票");
                tickets--;
            } finally {
                lock.unlock();   
                //必须这样做。因为最后一次tickets=0时，直接执行break，那么就没有释放锁，循环就不会停。
                //加上try finally就能保证一定会释放锁
            }
        }
    }
}
```

### 死锁

由于两个或者多个线程互相持有对方所需要的资源，导致这些线程处于等待状态，无法前往执行 

产生死锁的情况: 同步嵌套

## 线程通信

确保线程能够按照预定的顺序执行，并且能够安全地访问共享资源。使多条线程更好的进行协同工作

**等待唤醒机制**

| 成员方法          | 说明                   |
| ----------------- | ---------------------- |
| void  wait()      | 使当前线程等待         |
| void notify();    | 随机唤醒单个等待的线程 |
| void notifyAll(); | 唤醒所有等待的线程     |

这些方法需要使用锁对象调用。wait() 方法在等待的时候会释放锁对象。

```java
    /*
        两条线程通信
     */
    public static void main(String[] args) {
        Printer1 p = new Printer1();

        new Thread(new Runnable() {
            @Override
            public void run() {
                while (true) {
                    synchronized (Printer1.class) {
                        try {
                            p.print1();
                        } catch (InterruptedException e) { //子类在重写父类方法的时候，不能抛出父类没有的或者比父类更大																的异常。所以这里没法抛，只能try catch
                            e.printStackTrace();
                        }
                    }
                }
            }
        }).start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                while (true) {
                    synchronized (Printer1.class) {
                        try {
                            p.print2();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }
        }).start();

    }
}

class Printer1 {

    int flag = 1;

    public void print1() throws InterruptedException {

        if(flag != 1){
            Printer1.class.wait();
        }

        System.out.print("传");
        System.out.print("智");
        System.out.print("教");
        System.out.print("育");
        System.out.println();

        flag = 2;
        Printer1.class.notify();

    }

    public void print2() throws InterruptedException {

        if(flag != 2){
            Printer1.class.wait();
        }

        System.out.print("黑");
        System.out.print("马");
        System.out.print("程");
        System.out.print("序");
        System.out.print("员");
        System.out.println();

        flag = 1;
        Printer1.class.notify();

    }
```

```java
    /*
        三条线程通信

        问题:   sleep方法和wait方法的区别?
        回答:
                sleep方法是线程休眠, 时间到了自动醒来, sleep方法在休眠的时候, 不会释放锁.
                wait方法是线程等待, 需要由其它线程进行notify唤醒, wait方法在等待期间, 会释放锁.
     */
    public static void main(String[] args) {

        Printer2 p = new Printer2();

        new Thread(new Runnable() {
            @Override
            public void run() {
                while (true) {
                    synchronized (Printer2.class) {
                        try {
                            p.print1();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }
        }).start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                while (true) {
                    synchronized (Printer2.class) {
                        try {
                            p.print2();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }
        }).start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                while (true) {
                    synchronized (Printer2.class) {
                        try {
                            p.print3();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }
        }).start();
    }

}

class Printer2 {

    int flag = 1;

    public void print1() throws InterruptedException {

        while (flag != 1) {
            Printer2.class.wait();
        }

        System.out.print("传");
        System.out.print("智");
        System.out.print("教");
        System.out.print("育");
        System.out.println();

        flag = 2;
        Printer2.class.notifyAll();

    }

    public void print2() throws InterruptedException {

        while (flag != 2) {
            Printer2.class.wait();
        }

        System.out.print("黑");
        System.out.print("马");
        System.out.print("程");
        System.out.print("序");
        System.out.print("员");
        System.out.println();

        flag = 3;
        Printer2.class.notifyAll();

    }

    public void print3() throws InterruptedException {

        while (flag != 3) {
            Printer2.class.wait();
        }

        System.out.print("传");
        System.out.print("智");
        System.out.print("大");
        System.out.print("学");
        System.out.println();

        flag = 1;
        Printer2.class.notifyAll();

    }
```

使用 ReentrantLock 实现同步，并获取 Condition 对象

| 成员方法       | 说明                   |
| -------------- | ---------------------- |
| void  await()  | 指定线程等待           |
| void signal(); | 指定唤醒单个等待的线程 |

```java
    /*
        三条线程通信 - 优化
     */
    public static void main(String[] args) {

        Printer3 p = new Printer3();

        new Thread(new Runnable() {
            @Override
            public void run() {
                while (true) {
                    try {
                        p.print1();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }).start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                while (true) {
                    try {
                        p.print2();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }).start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                while (true) {
                    try {
                        p.print3();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }).start();
    }

}

class Printer3 {

    ReentrantLock lock = new ReentrantLock();

    Condition c1 = lock.newCondition();
    Condition c2 = lock.newCondition();
    Condition c3 = lock.newCondition();

    int flag = 1;

    public void print1() throws InterruptedException {
        lock.lock();

        if (flag != 1) {
            // 线程1等待, c1第一次调用await，c1绑定线程1
            c1.await();
        }

        System.out.print("传");
        System.out.print("智");
        System.out.print("教");
        System.out.print("育");
        System.out.println();

        flag = 2;
        c2.signal(); //在没有建立绑定关系的时候随机唤醒或空唤醒

        lock.unlock();
    }

    public void print2() throws InterruptedException {
        lock.lock();

        if (flag != 2) {
            // 线程2等待, c2第一次调用await，c2绑定线程2
            c2.await();
        }

        System.out.print("黑");
        System.out.print("马");
        System.out.print("程");
        System.out.print("序");
        System.out.print("员");
        System.out.println();

        flag = 3;
        c3.signal();

        lock.unlock();
    }

    public void print3() throws InterruptedException {
        lock.lock();

        if (flag != 3) {
            // 线程3等待, c3第一次调用await，c3绑定线程3
            c3.await();
        }

        System.out.print("传");
        System.out.print("智");
        System.out.print("大");
        System.out.print("学");
        System.out.println();

        flag = 1;
        c1.signal();

        lock.unlock();
    }
```

### 生产者消费者模式

![image-20241202153843966](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202412021538343.png)

```java
public class WareHouse {
    public static boolean mark = false;
    public static ReentrantLock lock = new ReentrantLock();
    public static Condition producer = lock.newCondition();
    public static Condition consumer = lock.newCondition();
}

public class Producer implements Runnable {
    @Override
    public void run() {
        while (true) {
            WareHouse.lock.lock();

            if(WareHouse.mark){
                // true : 说明有包子, 线程进入等待状态
                try {
                    WareHouse.producer.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            } else {
                // false : 没有包子, 生产包子, 改变Mark的状态, 唤醒消费者线程
                System.out.println("生产者线程生产包子...");
                WareHouse.mark = true;
                WareHouse.consumer.signal();
            }

            WareHouse.lock.unlock();
        }
    }
}


public class Consumer implements Runnable {
    @Override
    public void run() {
        while (true) {
            WareHouse.lock.lock();

            if(WareHouse.mark){
                // true : 说明有包子, 开吃, 改变mark的状态, 唤醒生产者线程
                System.out.println("消费者线程吃包子...");
                WareHouse.mark = false;
                WareHouse.producer.signal();
            } else {
                // false : 没有包子, 消费者线程等待
                try {
                    WareHouse.consumer.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }

            WareHouse.lock.unlock();
        }
    }
}

public class Test {
    public static void main(String[] args) {
        new Thread(new Producer()).start();
        new Thread(new Consumer()).start();
    }
}

```

## 线程生命周期

线程被创建并启动以后，它并不是一启动就进入了执行状态，也不是一直处于执行状态。线程对象在不同的时期有不同的状态 。

| **状态**                  | **具体含义**                                  |
| ------------------------- | --------------------------------------------- |
| NEW（新建）               | 创建线程对象                                  |
| RUNNABLE（就绪）          | start  方法被调用，但是还没有抢到 CPU  执行权 |
| BLOCKED（阻塞）           | 线程开始运行，但是没有获取到锁对象            |
| WAITING（等待）           | wait  方法                                    |
| TIMED_WAITING（计时等待） | sleep  方法                                   |
| TERMINATED（结束状态）    | 代码全部运行完毕                              |

![image-20241202154157890](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202412021541058.png)



Java 中的线程状态被定义在了 java.lang.Thread.State 枚举类 

![image-20241202154218805](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202412021542955.png)

## 线程池

将线程对象交给线程池维护可以降低系统成本 ，从而提升程序的性能

系统创建一个线程的成本是比较高的，因为它涉及到与操作系统交互。当程序中需要创建大量生存期很短暂的线程时，频繁的创建和销毁线程，就会严重浪费系统资源

![image-20241202154520076](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202412021545419.png)

![image-20241202154556990](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202412021545292.png)

### JDK提供的线程池

Executors 中提供静态方法来创建线程池

| **方法**                                         | **介绍**                         |
| ------------------------------------------------ | -------------------------------- |
| static  ExecutorService  newCachedThreadPool  () | 创建一个默认的线程池             |
| static  newFixedThreadPool  (int nThreads)       | 创建一个指定最多线程数量的线程池 |

```java
    /*
        JDK自带线程池

        Executors 中提供静态方法来创建线程池
            static ExecutorService newCachedThreadPool()  创建一个默认的线程池
            static newFixedThreadPool (int nThreads)      创建一个指定最多线程数量的线程池
     */
    public static void main(String[] args) {
        // 1. 获取线程池对象
        ExecutorService pool = Executors.newFixedThreadPool(10);

        // 2. 提交线程任务
        for (int i = 1; i <= 100; i++) {
            pool.submit(new Runnable() {
                @Override
                public void run() {
                    System.out.println(Thread.currentThread().getName() + "提交了线程任务");
                }
            });
        }

        pool.shutdown();
    }
```



### 自定义线程池

ThreadPoolExecutor 类

![image-20241202154744301](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202412021547454.png)

参数1：核心线程数量 (正式员工)。不能小于0

参数2：最大线程数量 (正式员工 + 临时工)。不能小于等于0, 最大数量 >= 核心线程数量

参数3：空闲时间。不能小于0

参数4：时间单位

参数5：任务队列。不能为null

参数6：线程对象任务工厂。不能为null

参数7：拒绝策略。不能为null

**拒绝策略**

| 策略选项                               | 说明                                                         |
| -------------------------------------- | ------------------------------------------------------------ |
| ThreadPoolExecutor.AbortPolicy         | 丢弃最新提交的任务并抛出RejectedExecutionException异常 (默认) |
| ThreadPoolExecutor.DiscardPolicy       | 丢弃任务，但是不抛出异常 这是不推荐的做法                    |
| ThreadPoolExecutor.DiscardOldestPolicy | 抛弃队列中等待最久的任务 然后把当前任务加入队列中            |
| ThreadPoolExecutor.CallerRunsPolicy    | 调用任务的run()方法, 绕过线程池直接执行                      |

```java
    /*
        自定义线程池对象

        参数5: 任务队列
                1) 有界队列     new ArrayBlockingQueue<>(10)
                2) 无界队列     new LinkedBlockingDeque<>()
     */
    public static void main(String[] args) {
        ThreadPoolExecutor pool = new ThreadPoolExecutor(
                2,
                5,
                60,
                TimeUnit.SECONDS,
                new ArrayBlockingQueue<>(10),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.CallerRunsPolicy()
        );

        for(int i = 1; i <= 16; i++){
            pool.submit(new Runnable() {
                @Override
                public void run() {
                    System.out.println(Thread.currentThread().getName() + "提交了任务");
                }
            });
        }
    }
```



## 单例设计模式

单例指单个实例，保证类的对象在内存中只有一份

使用场景: 

如果创建一个对象需要消耗的资源过多，比如 I/O 与数据库的连接

并且这个对象完全是可以复用的, 我们就可以考虑将其设计为单例的对象

**实现单例设计模式**

饿汉式

懒汉式 (延迟加载模式)

```java
    /*
        单例设计模式 - 饿汉式

        -----------------------------------------------------------------
		
        class Single1 {
            private Single1() {
            }
			// static保证可以通过类名.s的形式拿到同一个s。final保证拿到s后无法修改s。
			// 比如： Single1 s = Single1.s;
            public static final Single1 s = new Single1();
        }

        -----------------------------------------------------------------
     */
    public static void main(String[] args) {
        Single1 s = Single1.getInstance();
    }
}
// 改为面向对象版本：
class Single1 {
    // private保证别人不能随便用构造方法new出很多Single1
    private Single1() {
    }
	// static保证getInstance这个静态方法可以返回静态变量s
    private static Single1 s = new Single1();

    public static Single1 getInstance() {
        return s;
    }
}
```

```java
    /*
        单例设计模式 - 懒汉式 (延迟加载模式)

        --------------------------------------------------

            class Single2 {

                private Single2() {
                }

                private static Single2 s;

                public static Single2 getInstance() {
                    if (s == null) { // 为了保证单例，不然调用两次getInstance就会new出来两个不同的对象
                        s = new Single2();
                    }
                    return s;
                }

            }

            弊端: 在多线程并发操作的时候, 有可能创建出多个对象.

        --------------------------------------------------

            class Single2 {

                private Single2() {
                }

                private static Single2 s;

                public static Single2 getInstance() {
                    synchronized (Single2.class) {
                        if (s == null) {
                            s = new Single2();
                        }
                    }
                    return s;
                }

            }

            弊端: 效率非常低

        --------------------------------------------------
     */
    public static void main(String[] args) {
        for (int i = 1; i <= 10; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    Single2 s = Single2.getInstance();
                    System.out.println(s);
                }
            }).start();
        }
    }
}

class Single2 {

    private Single2() {
    }

    private static Single2 s;

    public static Single2 getInstance() {
        // 线程2
        // 线程1
        if (s == null) {
            synchronized (Single2.class) {
                if (s == null) {
                    s = new Single2();
                }
            }
        }

        return s;
    }
```

# 日志框架

## JUL

JavaSE平台提供的官方日志框架（java.util.logging）。配置相对简单，但不够灵活，性能较差。

## Log4j

一个流行的日志框架，提供灵活的配置选项，支持多种输出目标

## Logback

基于Log4j升级而来，提供了更多的功能和配置选项，性能优于Log4j

## Slf4j

Log4j和Logback是他的实现类。

简单的日志门面，提供了一套日志操作的标准接口及抽象类，允许应用程序使用不同的底层日志框架

# 枚举

- 格式：

```java
修饰符 enum 枚举类名{
    枚举类1，枚举类2，枚举类3...
}
enum Season{
    SPRING, SUMMER, AUTUMN, WINTER;
}
```

- 特点
  - 每一个枚举项其实就是该枚举的一个对象
  - 通过枚举类名去访问指定的枚举项 
  - 所有枚举类都是 Enum 的子类
  - 枚举也是类, 可以定义成员变量
  - 枚举类的第一行上必须是枚举项，最后一个枚举项后的分号是可以省略的。但是如果枚举类有其他的东西，这个分号就不能省略 (建议不要省略)
  - 枚举类可以有构造器，但必须是 private 的，它默认也是 private 
  - 枚举类也可以有抽象方法，但是枚举项必须重写该方法

```java
enum Season{
    SPRING("春天"){
    	@Overrided
        public void show(){
            ...
        }
    },
    SUMMER("夏天"), 
    AUTUMN("秋天"), 
    WINTER("冬天");
    String name;
    private Season(String name){
        this.name = name;
    }
    public abstract void show();
}
```

# 类加载器

将类的字节码文件加载到方法区

- 类加载器的加载过程

  ![image-20250422143124319](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504221431680.png)

- 类加载器的分类

  ![image-20250422143655030](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504221436143.png)

```java
public Classloader getClassLoader() //获取该类的, 类加载器对象.   .class字节码对象调用
```

- 双亲委派模式

  如果一个类加载器收到了类加载的请求，它并不会自己先加载，而是先把这个请求委托给它的父类加载器去执行，如果这个父类加载器还存在其他父类加载器，则进一步向上委托，依次递归，请求最终会到达顶层的启动类加载器，如果父类加载器可以完成类加载任务，就成功返回。如果父类加载器无法完成此加载任务，子加载器才会尝试自己去加载，这就是双亲委派模式。可以避免类的重复加载。

  ![image-20250422145031331](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504221450505.png)

- ClassLoader的常用方法

  ```java
  public static ClassLoader getSystemClassLoader() // 获取系统类加载器
  public InputStream getResourceAsStream(String name) // 加载某一个资源文件
  ```

  ```java
  ClassLoader systemClassLoader = ClassLoader.getSystemClassLoader();
  InputStream is = systemClassLoader.getResourceAsStream(config.properties);
  // 系统类加载器是用来加载我们自己写的类，而我们自己写的类在src下，所以他就会直接在src下找，所以我们需要把文件放到src下面，同时不需要写绝对或相对路径，而只用写文件名即可。
  ```

# 反射

```java
public class Student {
       private Integer age;
       private String name;

       public Student() {
       }
    
       private Student(Integer age) {
           this.age = age;
       }

       public Student(Integer age, String name) {
           this.age = age;
           this.name = name;
       }

       /**
        * 获取
        * @return age
        */
       public Integer getAge() {
           return age;
       }

       /**
        * 设置
        * @param age
        */
       public void setAge(Integer age) {
           this.age = age;
       }

       /**
        * 获取
        * @return name
        */
       public String getName() {
           return name;
       }

       /**
        * 设置
        * @param name
        */
       public void setName(String name) {
           this.name = name;
       }

       public String toString() {
           return "Student{age = " + age + ", name = " + name + "}";
       }
   }

```

## 字节码文件对象

**获取字节码文件对象的三种方法：**

```java
//1.        
Class aClass = Class.forName("com.wsb.Student"); //全类名
//2.   
Class bClass = Student.class;
//3.   
Student student = new Student();
Class cClass = student.getClass();
//因为class文件在硬盘中是唯一的，所以，当这个文件加载到内存之后产生的对象也是唯一的
```

## 构造方法

**获取构造方法：**

| 方法名                                                       | 说明                              |
| ------------------------------------------------------------ | --------------------------------- |
| Constructor<?>[] getConstructors()                           | 获得所有的构造（只能public修饰）  |
| Constructor<?>[] getDeclaredConstructors()                   | 获得所有的构造（包含private修饰） |
| Constructor<T> getConstructor(Class<?>... parameterTypes)    | 获取指定构造（只能public修饰）    |
| Constructor<T> getDeclaredConstructor(Class<?>... parameterTypes) | 获取指定构造（包含private修饰）   |

```java
		Class aClass = Class.forName("com.wsb.Student");

		//1.Constructor<?>[] getConstructors()获得所有的构造（只能public修饰）
        Constructor[] constructors1 = aClass.getConstructors();
        for (Constructor constructor : constructors1) {
            System.out.println(constructor);
        }
        //运行结果：
        //public com.wsb.Student(java.lang.Integer,java.lang.String)
		//public com.wsb.Student()

		//2.Constructor<?>[] getDeclaredConstructors()获得所有的构造（包含private修饰）
		Constructor[] declaredConstructors = aClass.getDeclaredConstructors();
        for (Constructor declaredConstructor : declaredConstructors) {
            System.out.println(declaredConstructor);
        }
        //运行结果：
        //public com.wsb.Student(java.lang.Integer,java.lang.String)
        //private com.wsb.Student(java.lang.Integer)
        //public com.wsb.Student()
		
		//3.Constructor<T> getConstructor(Class<?>... parameterTypes)获取指定构造（只能public修饰）
        Constructor constructor1 = aClass.getConstructor();
        System.out.println(constructor1);
		//运行结果：
		//public com.wsb.Student()
        Constructor constructor2 = aClass.getConstructor(Integer.class,String.class); //这里的传参顺序，必须和构造方法中的一样！
        System.out.println(constructor2);
		//运行结果：
		//public com.wsb.Student(java.lang.Integer,java.lang.String)

		//4.Constructor<T> getDeclaredConstructor(Class<?>... parameterTypes)获取指定构造（包含private修饰）
		Constructor declaredConstructor1 = aClass.getDeclaredConstructor();
        System.out.println(declaredConstructor1);
		//运行结果：
		//public com.wsb.Student()

        Constructor declaredConstructor2 = aClass.getDeclaredConstructor(Integer.class);
        System.out.println(declaredConstructor2);
		//运行结果：
		//private com.wsb.Student(java.lang.Integer)

		System.out.println(constructor1 == declaredConstructor1); //每一次获取构造方法对象的时候，都会新new一个。
		//运行结果：false
```

## 创建对象

**获取构造方法并创建对象**：

```java
        Class aClass = Class.forName("com.wsb.Student");
        Constructor constructor = aClass.getConstructor();
        Student student = (Student)constructor.newInstance();
        System.out.println(student);
		//运行结果：
		//Student{age = null, name = null}

		Constructor declaredConstructor = aClass.getDeclaredConstructor(Integer.class);
		//临时修改构造方法的访问权限（暴力反射）
        declaredConstructor.setAccessible(true);
        Student student1 = (Student)declaredConstructor.newInstance(22);
        System.out.println(student1);
		//运行结果：
		//Student{age = 22, name = null}
```

## 成员变量

**获取成员变量：**

| 方法名                              | 说明                                         |
| ----------------------------------- | -------------------------------------------- |
| Field[] getFields()                 | 返回所有成员变量对象的数组（只能拿public的） |
| Field[] getDeclaredFields()         | 返回所有成员变量对象的数组，存在就能拿到     |
| Field getField(String name)         | 返回单个成员变量对象（只能拿public的）       |
| Field getDeclaredField(String name) | 返回单个成员变量对象，存在就能拿到           |

```java
        Class aClass = Class.forName("com.wsb.Student");
        //1.Field[] getFields()返回所有成员变量对象的数组（只能拿public的）
		Field[] fields = aClass.getFields();
        for (Field field : fields) {
            System.out.println(field);
        }
        //2.Field[] getDeclaredFields()返回所有成员变量对象的数组，存在就能拿到
        Field[] declaredFields = aClass.getDeclaredFields();
        for (Field declaredField : declaredFields) {
            System.out.println(declaredField);
        }
        //3.Field getField(String name)返回单个成员变量对象（只能拿public的）
        Field age = aClass.getField("age");
        System.out.println(age);
        //4.Field getDeclaredField(String name)返回单个成员变量对象，存在就能拿到
        Field name = aClass.getDeclaredField("name");
        System.out.println(name);
```

## 值

**获取成员变量并获取值和修改值：**

| 方法                                | 说明   |
| ----------------------------------- | ------ |
| void set(Object obj, Object value） | 赋值   |
| Object get(Object obj)              | 获取值 |

```java
        Student student1 = new Student(23, "张三");
        Student student2 = new Student(24, "李四");
        Class aClass = Class.forName("com.wsb.Student");
        Field name = aClass.getDeclaredField("name");
        name.setAccessible(true);
        name.set(student1,"王五");
        String result = (String)name.get(student1);
        System.out.println(result);
        System.out.println(student1);
        System.out.println(student2);
		//运行结果：
		//王五
		//Student{age = 23, name = 王五}
		//Student{age = 24, name = 李四}
```

## 成员方法

**获取成员方法：**

| 方法名                                                       | 说明                                         |
| ------------------------------------------------------------ | -------------------------------------------- |
| Method[] getMethods()                                        | 返回所有成员方法对象的数组（只能拿public的） |
| Method[] getDeclaredMethods()                                | 返回所有成员方法对象的数组，存在就能拿到     |
| Method getMethod(String name, Class<?>... parameterTypes)    | 返回单个成员方法对象（只能拿public的）       |
| Method getDeclaredMethod(String name, Class<?>... parameterTypes) | 返回单个成员方法对象，存在就能拿到           |

## 运行

**获取成员方法并运行：**

| **方法**                                   | **功能** |
| ------------------------------------------ | -------- |
| Object  invoke(Object obj, Object... args) | 运行方法 |

# 代理

为什么需要代理？

代理可以无侵入式的给对象增强其他的功能。

```java
public class Test {
    public static void main(String[] args) {
        // 准备一个明星对象
        Star star = new Star("章若楠");
        // 为明星创建一个专属于她的代理对象
        StarService proxy = ProxyUtil.createProxy(star);
        proxy.sing("红昭愿");
        System.out.println(proxy.dance());
    }
}

@Data
@AllArgsConstructor
@NoArgsConstructor
public class Star implements StarService{
    private String name;

    @Override
    public void sing(String name) {
        System.out.println(this.name + "表演唱歌:" + name);
    }

    @Override
    public String dance() {
        System.out.println(this.name + "表演跳舞:魅力四射");
        return "谢谢！";
    }
}

public interface StarService {
    void sing(String name);
    String dance();
}

/*
    代理工具类：中介公司，专门负责创建代理对象并返回给别人使用
 */
public class ProxyUtil {
    // 创建一个明星的代理对象返回
    public static StarService createProxy(Star s){
        /**
         * java.lang.reflect.Proxy类：提供了为对象产生代理对象的方法
         *  public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandlder h)
         *  参数一：指定用哪个类加载器去加载生成的代理类。
         *  参数二：用于指定代理类需要实现的接口。明星类实现了哪些接口，代理类就实现哪些接口。
         *  参数三：用来指定生成的代理对象要干什么事情。
         */
        StarService proxy = (StarService) Proxy.newProxyInstance(
                ProxyUtil.class.getClassLoader(),
                s.getClass().getInterfaces(),
                new InvocationHandler() {
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        /**
                         * 参数一：接收到代理对象本身（暂时用处不大）
                         * 参数二：正在被代理的方法
                         * 参数三：正在被代理的方法的参数
                         */
                        if ("sing".equals(method.getName())){
                            System.out.println("准备话筒，收钱");
                        } else if ("dance".equals(method.getName())) {
                            System.out.println("准备场地，收钱");
                        }
                        // 真正干活，把真正的明星对象叫过来正式干活
                        // 找真正的明星对象来执行被代理的行为：method方法
                        Object result = method.invoke(s, args);
                        return result;
                    }
                }
        );
        return proxy;
    }
}
```





































 



















