### spring 中的 bean 是线程安全的吗？
答：spring 中的 bean 默认是单例模式，spring 框架并没有对单例 bean 进行多线程的封装处理。
实际上大部分时候 spring bean 无状态的（比如 dao 类），所有某种程度上来说 bean 也是安全的，但如果 bean 有状态的话（比如 view model 对象），那就要开发者自己去保证线程安全了，最简单的就是改变 bean的作用域，把“singleton”变更为“prototype”，这样请求 bean 相当于 new Bean()了，所以就可以保证线程安全了。有状态就是有数据存储功能。无状态就是不会保存数据

### spring 支持几种 bean 的作用域？
spring 支持 5 种作用域，如下：
1. singleton：spring ioc 容器中只存在一个 bean 实例，bean 以单例模式存在，是系统默认值；
2. prototype：每次从容器调用 bean 时都会创建一个新的示例，既每次 getBean()相当于执行 new Bean()操作；

Web 环境下的作用域：
3. request：每次 http 请求都会创建一个 bean；
4. session：同一个 http session 共享一个 bean 实例；
5. global-session：用于 portlet 容器，因为每个 portlet 有单独的 session，globalsession 提供一个全局性的 http session。

注意： 使用 prototype 作用域需要慎重的思考，因为频繁创建和销毁 bean会带来很大的性能开销。

###  spring 自动装配 bean 有哪些方式？
答：
- no：默认值，表示没有自动装配，应使用显式 bean 引用进行装配。
- byName：它根据 bean 的名称注入对象依赖项。
- byType：它根据类型注入对象依赖项。
- 构造函数：通过构造函数来注入依赖项，需要设置大量的参数。
- autodetect：容器首先通过构造函数使用 autowire 装配，如果不能，则通过 byType 自动装配。

### 说一下 spring mvc 运行流程？
1. spring mvc 先将请求发送给 DispatcherServlet。
2. DispatcherServlet 查询一个或多个 HandlerMapping，找到处理请求的 Controller。
3. DispatcherServlet 再把请求提交到对应的 Controller。
4. Controller 进行业务逻辑处理后，会返回一个 ModelAndView。
5. Dispathcher 查询一个或多个ViewResolver视图解析器，找到ModelAndView对象指定的视图对象。6. 视图对象负责渲染返回给客户端

### @RequestMapping 的作用是什么？
答：将 http 请求映射到相应的类/方法上。

### spring boot 核心配置文件是什么？
答：
1. bootstrap (. yml 或者 . properties)：boostrap 由父 ApplicationContext 加载的，比 applicaton 
优先加载，且 boostrap 里面的属性不能被覆盖；
2. application (. yml 或者 . properties)：用于 spring boot 项目的自动化配置。

### spring boot 配置文件有哪几种类型？它们有什么区别？
答：配置文件有 . properties 格式和 . yml 格式，它们主要的区别是书法风格不同。

. properties 配置如下：
spring. RabbitMQ. port=5672

. yml 配置如下：

spring:

RabbitMQ:

port: 5672

. yml 格式不支持 @PropertySource 注解导入。



![image-20210817174909624](../../../asserts/Spring面试题/image-20210817174909624.png)

![image-20210817174850028](../../../asserts/Spring面试题/image-20210817174850028.png)