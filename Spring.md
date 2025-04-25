# 工厂设计模式

## 1.什么是⼯⼚设计模式

- 概念：通过⼯⼚类，创建对象。

而不再：

```java
User user = new User();
UserDAO userDAO = new UserDAOImpl();
```

- 好处：解耦合

耦合：代码间的强关联关系，⼀⽅的改变会影响到另⼀⽅

问题：不利于代码维护。把接⼝的实现类，硬编码在程序中。

```java
 UserService userService = new UserServiceImpl();
```

## 2.简单工厂的设计

```java
import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;

public class BeanFactory {
    private static Properties env = new Properties();

    static {
        try {
            // 第一步：获得IO输入流，读取 applicationContext.properties 文件
            InputStream inputStream = BeanFactory.class.getResourceAsStream("/applicationContext.properties");

            // 第二步：文件内容封装到 Properties 集合中
            // key = userService, value = com.baizhiedu.basic.UserServiceImpl
            env.load(inputStream);
            inputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

	/*
		对象的创建⽅式：
			1. 直接调⽤构造⽅法 创建对象  UserService userService = new UserServiceImpl();
 			2. 通过反射的形式 创建对象 解耦合
				Class clazz = Class.forName("com.baizhiedu.basic.UserServiceImpl");
 				UserService userService = (UserService)clazz.newInstance();
 	*/
    public static UserService getUserService() {
        UserService userService = null;
        try {
            // 从配置文件中读取类名
            // 如果不写到配置文件中，那这里就要：Class.forName(com.baizhiedu.basic.UserServiceImpl); 还是耦合。
            Class<?> clazz = Class.forName(env.getProperty("userService"));
            userService = (UserService) clazz.newInstance();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }

        return userService;
    }

    // 通过反射创建 UserDAO 对象
    public static UserDAO getUserDAO() {
        UserDAO userDAO = null;
        try {
            Class<?> clazz = Class.forName(env.getProperty("userDAO"));
            userDAO = (UserDAO) clazz.newInstance();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }

        return userDAO;
    }
}

```

## 3.通用工厂的设计

- 问题：简单⼯⼚会存在⼤量的代码冗余

![image-20250406183239909](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504061832009.png)

- 通⽤⼯⼚的代码

```java
import java.io.InputStream;
import java.util.Properties;

public class BeanFactory {
    private static Properties env = new Properties();

    static {
        try {
            // 加载配置文件
            InputStream inputStream = BeanFactory.class.getResourceAsStream("/applicationContext.properties");
            env.load(inputStream);
            inputStream.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // 通用工厂方法，根据 key 获取对应对象实例
    public static Object getBean(String key) {
        Object ret = null;
        try {
            Class<?> clazz = Class.forName(env.getProperty(key));
            ret = clazz.newInstance();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return ret;
    }
}

```

- 通用工厂的使用方式

  ```java
  1. 定义类型 (类)
  2. 通过配置⽂件的配置告知⼯⼚(applicationContext.properties)
     key = value
  3. 通过⼯⼚获得类的对象
     Object ret = BeanFactory.getBean("key")
  ```

## 4.Spring本质

⼯⼚：ApplicationContext 

配置文件：applicationContext.xml 

# 第一个Spring程序

## 1.配置文件

-  配置⽂件的放置位置：任意位置 没有硬性要求
-  配置⽂件的命名：没有硬性要求  建议：applicationContext.xml

## 2.核心API

- ApplicationContext

  作用：Spring提供ApplicationContext作为⼯⼚，⽤于对象的创建

  好处：解耦合

  - ApplicationContext接⼝类型

    ```
    1.接⼝：屏蔽实现的差异
    2.⾮web环境 ： ClassPathXmlApplicationContext (main junit)
    3.web环境  ：  XmlWebApplicationContext
    ```
    
    ![](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504061841645.png)
    
  - 重量级资源
  
    ```
    ApplicationContext⼯⼚的对象占⽤⼤量内存。
    所以，不会频繁的创建对象 ： ⼀个应⽤只会创建⼀个⼯⼚对象。
    所以，ApplicationContext⼯⼚：⼀定是线程安全的(多线程并发访问)
    ```

## 3.程序

```java
// 1. 创建类型

// 2. 配置文件的配置 applicationContext.xml
// <bean id="person" class="com.baizhiedu.basic.Person"/>

// 3. 通过工厂类，获得对象
// ApplicationContext
//     |- ClassPathXmlApplicationContext
ApplicationContext ctx = new ClassPathXmlApplicationContext("/applicationContext.xml");
Person person = (Person) ctx.getBean("person");
```

# 反转控制（IOC）

- 控制：对于成员变量赋值的控制权 

- 反转控制：把对于成员变量赋值的控制权，从代码中反转(转移)到Spring⼯⼚和配置⽂件中完成   

- 好处：解耦合 

- 底层实现：工厂设计模式

  ![image-20250425124125414](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504251241707.png)

# 依赖注入（DI）

- 注⼊：通过Spring的工厂及配置文件，为对象（bean，组件）的成员变量赋值 

- 依赖注⼊：当⼀个类需要另⼀个类时，就意味着依赖，⼀旦出现依赖，就可以把另⼀个类作为本类的成员变量，最终通过Spring配置⽂件进行注入(赋值)。   

- 好处：解耦合

![image-20250425124200864](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202504251242989.png)













# IOC

## bean的基础配置(id,class)

```xml
<!--
	配置文件中完成bean的配置
	bean标签标示配置bean
    id属性标示给bean起名字，使用容器可以通过id值获取对应的bean，在一个容器中id值唯一
    class属性表示给bean定义类型，即配置的bean的全路径类名
-->
	<bean id="bookDao" class="com.wsb.dao.impl.BookDaoImpl"/>
    <bean id="bookService" class="com.wsb.service.impl.BookServiceImpl"/>
```

```java
//获取IOC容器
ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml"); 
BookService bookService = (BookService) ctx.getBean("bookService");
bookService.save();
```

### bean的name属性(配置别名)

```xml
    <!--name:为bean指定别名，别名可以有多个，使用逗号，分号，空格进行分隔-->
	<!--ref的属性值也可以是另一个bean的name属性值，不过建议使用其id进行注入-->
    <bean id="bookService" name="service service4 bookEbi" class="com.itheima.service.impl.BookServiceImpl">
        <property name="bookDao" ref="dao"/>
    </bean>
	
    <bean id="bookDao" name="dao" class="com.itheima.dao.impl.BookDaoImpl"/>
```

### bean的作用范围scope属性

```xml
    <!--scope：为bean设置作用范围，可选值为单例singloton（默认值），非单例prototype-->
    <bean id="bookDao" name="dao" scope="singloton" class="com.itheima.dao.impl.BookDaoImpl"/>
```

## bean实例化

### 构造方法实例化

```java
public class BookDaoImpl implements BookDao {
    private BookDaoImpl() {
        System.out.println("book dao constructor is running ....");
    }
    public void save() {
        System.out.println("book dao save ...");
    }
```

能访问到类中的私有构造方法,显而易见Spring底层用的是反射。使用的是类的无参构造。

### 静态工厂实例化

#### 工厂方式创建bean

```java
public interface OrderDao {
    public void save();
}

public class OrderDaoImpl implements OrderDao {
    public void save() {
        System.out.println("order dao save ...");
    }
}
```

```java
//静态工厂创建对象
public class OrderDaoFactory {
    public static OrderDao getOrderDao(){
        return new OrderDaoImpl();
    }
}
```

```java
public class AppForInstanceOrder {
    public static void main(String[] args) {
        //通过静态工厂创建对象
        OrderDao orderDao = OrderDaoFactory.getOrderDao();
        orderDao.save();
    }
}
```

对象是通过上面的这种方式来创建的，如何将其交给Spring来管理呢?

#### 静态工厂实例化

```xml
<bean id="orderDao" class="com.itheima.factory.OrderDaoFactory" factory-method="getOrderDao"/>
```

class:工厂类的类全名 factory-mehod:具体工厂类中创建对象的方法名

![image-20241205095524515](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202412050955625.png)

看到这，可能有人会问了，你这种方式在工厂类中不也是直接new对象的，和我自己直接new没什么太大的区别，而且静态工厂的方式反而更复杂，这种方式的意义是什么?

主要的原因是:

- 在工厂的静态方法中，我们除了new对象还可以做其他的一些业务操作，这些操作必不可少,如:

```java
public class OrderDaoFactory {
    public static OrderDao getOrderDao(){
        System.out.println("factory setup....");//模拟必要的业务操作
        return new OrderDaoImpl();
    }
}
```

之前new对象的方式就无法添加其他的业务内容

### 实例工厂与FactoryBean

```java
public interface UserDao {
    public void save();
}

public class UserDaoImpl implements UserDao {

    public void save() {
        System.out.println("user dao save ...");
    }
}
```

创建一个工厂类OrderDaoFactory并提供一个普通方法，注意此处和静态工厂的工厂类不一样的地方是方法不是静态方法

```java
public class UserDaoFactory {
    public UserDao getUserDao(){
        return new UserDaoImpl();
    }
}
```

```java
public class AppForInstanceUser {
    public static void main(String[] args) {
        //创建实例工厂对象
        UserDaoFactory userDaoFactory = new UserDaoFactory();
        //通过实例工厂对象创建对象
        UserDao userDao = userDaoFactory.getUserDao();
        userDao.save();
}
```

对于这种实例工厂创建对象的方式如何交给Spring管理呢?

#### 实例工厂实例化

```xml
<bean id="userFactory" class="com.itheima.factory.UserDaoFactory"/>
<bean id="userDao" factory-method="getUserDao" factory-bean="userFactory"/>
```

实例化工厂运行的顺序是:

* 创建实例化工厂对象,对应的是第一行配置

* 调用对象中的方法来创建bean，对应的是第二行配置

  * factory-bean:工厂的实例对象

  * factory-method:工厂对象中的具体创建对象的方法名,对应关系如下:

![image-20241205100225971](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202412051002038.png)

factory-mehod:具体工厂类中创建对象的方法名

实例工厂实例化的方式配置的过程还是比较复杂，所以Spring为了简化这种配置方式就提供了一种叫`FactoryBean`的方式来简化开发

#### FactoryBean的使用

```java
public class UserDaoFactoryBean implements FactoryBean<UserDao> {
    //代替原始实例工厂中创建对象的方法
    public UserDao getObject() throws Exception {
        return new UserDaoImpl();
    }
    //返回所创建类的Class对象
    public Class<?> getObjectType() {
        return UserDao.class;
    }
}
```

```xml
<bean id="userDao" class="com.itheima.factory.UserDaoFactoryBean"/>
```

查看源码会发现，FactoryBean接口其实会有三个方法，分别是:

```java
T getObject() throws Exception;

Class<?> getObjectType();

default boolean isSingleton() {
		return true;
}
```

方法一:getObject()，被重写后，在方法中进行对象的创建并返回

方法二:getObjectType(),被重写后，主要返回的是被创建类的Class对象

方法三:没有被重写，因为它已经给了默认值，从方法名中可以看出其作用是设置对象是否为单例，默认true







# DI

## setter注入

### 注入引用数据类型

```java
public class BookServiceImpl implements BookService {
    private BookDao bookDao;
    public void setBookDao(BookDao bookDao) {
        this.bookDao = bookDao;
    }
}
```

配置中使用==property==标签==ref==属性注入引用类型对象

```xml
<bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
	<property name="bookDao" ref="bookDao"/>
</bean>

<bean id="bookDao" class="com.itheima.dao.imipl.BookDaoImpl"/>
```

![image-20241204172934549](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202412041729633.png)

### 注入简单数据类型

```xml
    <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl">
        <property name="databaseName" value="mysql"/>
     	<property name="connectionNum" value="10"/>
    </bean>
```

```java
public class BookDaoImpl implements BookDao {

    private String databaseName;
    private int connectionNum;

    public void setConnectionNum(int connectionNum) {
        this.connectionNum = connectionNum;
    }

    public void setDatabaseName(String databaseName) {
        this.databaseName = databaseName;
    }

    public void save() {
        System.out.println("book dao save ..."+databaseName+","+connectionNum);
    }
}
```

**说明:**

value:后面跟的是简单数据类型，对于参数类型，Spring在注入的时候会自动转换，但是不能写成

```xml
<property name="connectionNum" value="abc"/>
```

这样的话，spring在将`abc`转换成int类型的时候就会报错。

## 构造器注入

### 注入引用数据类型

```xml
    <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
    <bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
        <constructor-arg name="bookDao" ref="bookDao"/>
    </bean>
```

```java
public class BookServiceImpl implements BookService{
    private BookDao bookDao;

    public BookServiceImpl(BookDao bookDao) {
        this.bookDao = bookDao;
    }

    public void save() {
        System.out.println("book service save ...");
        bookDao.save();
    }
}
```

**说明:**

标签<constructor-arg>中

- name属性对应的值为构造函数中方法形参的参数名，必须要保持一致。
- ref属性指向的是spring的IOC容器中其他bean对象。

### 注入多个引用数据类型

```xml
    <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
    <bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl"/>
    <bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
        <constructor-arg name="bookDao" ref="bookDao"/>
        <constructor-arg name="userDao" ref="userDao"/>
    </bean>
```

```java
public class BookServiceImpl implements BookService{
    private BookDao bookDao;
    private UserDao userDao;

    public BookServiceImpl(BookDao bookDao,UserDao userDao) {
        this.bookDao = bookDao;
        this.userDao = userDao;
    }

    public void save() {
        System.out.println("book service save ...");
        bookDao.save();
        userDao.save();
    }
}
```

### 注入多个简单数据类型

```xml
    <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl">
        <constructor-arg name="databaseName" value="mysql"/>
        <constructor-arg name="connectionNum" value="666"/>
    </bean>
```

```java
public class BookDaoImpl implements BookDao {
    private String databaseName;
    private int connectionNum;

    public BookDaoImpl(String databaseName, int connectionNum) {
        this.databaseName = databaseName;
        this.connectionNum = connectionNum;
    }

    public void save() {
        System.out.println("book dao save ..."+databaseName+","+connectionNum);
    }
}
```

**问题：**

- 当构造函数中方法的参数名发生变化后，配置文件中的name属性也需要跟着变
- 这两块存在紧耦合，具体该如何解决?

方式一:删除name属性，添加type属性，按照类型注入

```xml
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl">
    <constructor-arg type="int" value="10"/>
    <constructor-arg type="java.lang.String" value="mysql"/>
</bean>
```

- 这种方式可以解决构造函数形参名发生变化带来的耦合问题
- 但是如果构造方法参数中有类型相同的参数，这种方式就不太好实现了

方式二:删除type属性，添加index属性，按照索引下标注入，下标从0开始

```xml
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl">
    <constructor-arg index="1" value="100"/>
    <constructor-arg index="0" value="mysql"/>
</bean>
```

- 这种方式可以解决参数类型重复问题
- 但是如果构造方法参数顺序发生变化后，这种方式又带来了耦合问题

### 自动装配

#### 按类型注入

```java
public interface BookService {
    public void save();
}

public class BookServiceImpl implements BookService{
    private BookDao bookDao;

    public void setBookDao(BookDao bookDao) {
        this.bookDao = bookDao;
    }

    public void save() {
        System.out.println("book service save ...");
        bookDao.save();
    }
}
```

```xml
    <bean class="com.itheima.dao.impl.BookDaoImpl"/>
    <!--autowire属性：开启自动装配，通常使用按类型装配-->
    <bean id="bookService" class="com.itheima.service.impl.BookServiceImpl" autowire="byType"/>
```

注意事项:

- 需要注入属性的类中对应属性的setter方法不能省略
- 被注入的对象必须要被Spring的IOC容器管理
- 按照类型在Spring的IOC容器中如果找到多个对象，会报`NoUniqueBeanDefinitionException`

#### 按名称注入

一个类型在IOC中有多个对象，还想要注入成功，这个时候就需要按照名称注入，配置方式为:

```xml
    <bean class="com.itheima.dao.impl.BookDaoImpl"/>
    <!--autowire属性：开启自动装配，通常使用按类型装配-->
    <bean id="bookService" class="com.itheima.service.impl.BookServiceImpl" autowire="byName"/>
```

注意事项:

- 按照名称注入中的名称指的是什么?

- ![image-20241204194757099](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202412041947132.png)
  - bookDao是private修饰的，外部类无法直接方法
  - 外部类只能通过属性的set方法进行访问
  - 对外部类来说，setBookDao方法名，去掉set后首字母小写是其属性名
    - 为什么是去掉set首字母小写?
    - 这个规则是set方法生成的默认规则，set方法的生成是把属性名首字母大写前面加set形成的方法名
  - 所以按照名称注入，其实是和对应的set方法有关，如果按照标准起名称，属性名和set对应的名是一致的
- 如果按照名称去找对应的bean对象，找不到则注入Null
- 当某一个类型在IOC容器中有多个对象，按照名称注入只找其指定名称对应的bean对象，不会报错 

两种方式介绍完后，以后用的更多的是按照类型注入。