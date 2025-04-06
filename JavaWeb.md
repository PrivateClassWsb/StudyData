# 统一拦截技术

## 过滤器Filter

![image-20241218151018806](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202412181510850.png)

### 快速入门

- 第1步，定义过滤器 ：定义一个类，实现 Filter 接口，并重写其所有方法。
- 第2步，配置过滤器：Filter类上加 @WebFilter 注解，配置拦截资源的路径。引导类上加 @ServletComponentScan 开启Servlet组件支持。

```java
//定义一个类，实现一个标准的Filter过滤器的接口
public class DemoFilter implements Filter {
    @Override //初始化方法, 只调用一次
    public void init(FilterConfig filterConfig) throws ServletException {
        System.out.println("init 初始化方法执行了");
    }

    @Override //拦截到请求之后调用, 调用多次
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        System.out.println("Demo 拦截到了请求...放行前逻辑");
        //放行
        chain.doFilter(request,response);
    }

    @Override //销毁方法, 只调用一次
    public void destroy() {
        System.out.println("destroy 销毁方法执行了");
    }
}
```

> - init方法：过滤器的初始化方法。在web服务器启动的时候会自动的创建Filter过滤器对象，在创建过滤器对象的时候会自动调用init初始化方法，这个方法只会被调用一次。
> - doFilter方法：这个方法是在每一次拦截到请求之后都会被调用，所以这个方法是会被调用多次的，每拦截到一次请求就会调用一次doFilter()方法。
> - destroy方法： 是销毁的方法。当我们关闭服务器的时候，它会自动的调用销毁方法destroy，而这个销毁方法也只会被调用一次。

在定义完Filter之后，Filter其实并不会生效，还需要完成Filter的配置，Filter的配置非常简单，只需要在Filter类上添加一个注解：@WebFilter，并指定属性urlPatterns，通过这个属性指定过滤器要拦截哪些请求

```java
@WebFilter(urlPatterns = "/*") //配置过滤器要拦截的请求路径（ /* 表示拦截浏览器的所有请求 ）
public class DemoFilter implements Filter {
    @Override //初始化方法, 只调用一次
    public void init(FilterConfig filterConfig) throws ServletException {
        System.out.println("init 初始化方法执行了");
    }

    @Override //拦截到请求之后调用, 调用多次
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        System.out.println("Demo 拦截到了请求...放行前逻辑");
        //放行
        chain.doFilter(request,response);
    }

    @Override //销毁方法, 只调用一次
    public void destroy() {
        System.out.println("destroy 销毁方法执行了");
    }
}
```

因为`@WebFilter` 是标准的 Servlet 注解，需要依赖 Servlet 容器直接管理。如果使用 `@WebFilter`，Spring 需要通过 `@ServletComponentScan` 扫描带有 `@WebFilter` 的过滤器，并将其注册到 Servlet 容器中。

这也是为什么，当我们在Filter类上面加了@WebFilter注解之后，接下来我们还需要在启动类上面加上一个注解@ServletComponentScan，通过这个@ServletComponentScan注解来开启SpringBoot项目对于Servlet组件的支持。

```java
@ServletComponentScan
@SpringBootApplication
public class MainApplication {

    public static void main(String[] args) {
        SpringApplication.run(MainApplication.class, args);
    }

}
```

### FilterRegistrationBean

在不依赖 `@WebFilter` 注解的情况下注册过滤器，并将其纳入 Spring 管理：通过 `FilterRegistrationBean` 注册过滤器。这种方式优先级高于 `@WebFilter`，因为它允许开发者直接在 Java 配置中对过滤器进行细粒度的控制，比如指定 URL 匹配模式、执行顺序等。

![image-20250102172150015](https://wsb-typora-picture.oss-cn-chengdu.aliyuncs.com/picgo/202501021721104.png)
