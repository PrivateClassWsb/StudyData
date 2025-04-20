# 网关

## Gateway

### 过滤器

- 路由过滤器

  配置在路由下的过滤器只对当前路由的请求生效。

  ```yml
  spring:
    cloud:
      gateway:
        routes:
        - id: user-service 
          uri: lb://userservice 
          predicates: 
          - Path=/user/** 
          filters: # 路由过滤器
          - AddRequestHeader=Truth, guanzhi is freaking awesome! # 添加请求头
  ```

- 默认过滤器

  对所有路由都生效的过滤器。

  ```yml
  spring:
    cloud:
      gateway:
        routes:
        - id: user-service 
          uri: lb://userservice 
          predicates: 
          - Path=/user/**
        default-filters: # 默认过滤器
        - AddRequestHeader=Truth, guanzhi is freaking awesome! 
  ```

  

- 自定义全局过滤器

  由于网关提供的每一种过滤器的作用都是固定的，如果我们希望拦截请求，做自己的业务逻辑则没办法实现，这时候就需要自定义全局过滤器。与默认过滤器作用相似，也是对所有路由都生效的过滤器。

  - 定义方式：需要实现GlobalFilter接口。

  ```java
  public interface GlobalFilter {
      /**
       *  处理当前请求，有必要的话通过{@link GatewayFilterChain}将请求交给下一个过滤器处理
       *
       * @param exchange 请求上下文，里面可以获取Request、Response等信息
       * @param chain 用来把请求委托给下一个过滤器 
       * @return {@code Mono<Void>} 返回标示当前过滤器业务结束
       */
      Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain);
  }
  ```

  ```java
  @Order(-1)
  @Component
  public class AuthorizeFilter implements GlobalFilter {
      @Override
      public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
          // 1.获取请求参数
          MultiValueMap<String, String> params = exchange.getRequest().getQueryParams();
          // 2.获取authorization参数
          String auth = params.getFirst("authorization");
          // 3.校验
          if ("admin".equals(auth)) {
              // 放行
              return chain.filter(exchange);
          }
          // 4.拦截
          // 4.1.禁止访问，设置状态码
          exchange.getResponse().setStatusCode(HttpStatus.FORBIDDEN);
          // 4.2.结束处理
          return exchange.getResponse().setComplete();
      }
  }
  ```

- 过滤器执行顺序

  - 每一个过滤器都必须指定一个int类型的order值，order值越小，优先级越高，执行顺序越靠前。


  - GlobalFilter通过实现Ordered接口，或者添加@Order注解来指定order值，由我们自己指定


  - 路由过滤器和defaultFilter的order由Spring指定，默认是按照声明顺序从1递增。


  - 当过滤器的order值一样时，会按照 defaultFilter > 路由过滤器 > GlobalFilter的顺序执行


### 跨域问题

- 跨域：域名不一致或端口不一致，比如：
  - 域名不同： www.taobao.com 和 www.taobao.org 和 www.jd.com 和 miaosha.jd.com
  - 域名相同，端口不同：localhost:8080和localhost:8081

- 跨域问题：**浏览器**禁止请求的发起者与服务端发生跨域**ajax请求**，请求被浏览器拦截的问题

- CORS方案

  ```yml
  spring:
    cloud:
      gateway:
        globalcors: # 全局的跨域处理
          add-to-simple-url-handler-mapping: true # 解决options请求被拦截问题（浏览器向服务器端询问“是否允许它跨域？”，这个请求就是options请求，正常情况下这个请求是被拦截的）
          corsConfigurations:
            '[/**]':	# 拦截哪些请求（这里表示拦截一切请求）
              allowedOrigins: # 允许哪些网站的跨域请求 
                - "http://localhost:8090"
              allowedMethods: # 允许的跨域ajax的请求方式
                - "GET"
                - "POST"
                - "DELETE"
                - "PUT"
                - "OPTIONS"
              allowedHeaders: "*" # 允许在请求中携带的头信息
              allowCredentials: true # 是否允许携带cookie
              maxAge: 360000 # 这次跨域检测的有效期
  ```

  