## 概念


Filter过滤器它是 JavaWeb 的三大组件之一。三大组件分别是：Sevlet程序、Filter过滤器、Listener监听器。Filter 过滤器它是JavaEE的规范，也就是接口。它的作用是：拦截请求、过滤响应。它的常见应用场景有：认证权限检查、日志记录等等。
## 工作流程

![image-20211029161427622](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20211029161427622.png)


## 使用步骤


我们现在基本都是使用Spring Boot来进行Web开发，使用Sevlet的比较少了。我们就在Spring Boot环境下进行演示了。假设现在需要实现一个记录接口运行时间的需求。实现如下：
​

```java
@RestController
public class TestController {

    private static final Logger logger = LoggerFactory.getLogger(TestController.class);

    @RequestMapping(value = "/test")
    public void test() throws InterruptedException {
        Thread.sleep(5000);
        logger.info("访问成功");
    }
}
```
编写一个controller请求测试
```java
public class RecordFilter implements Filter {

    private static final Logger logger = LoggerFactory.getLogger(RecordFilter.class);

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        logger.info("进入RecordFilter");
        long start = System.currentTimeMillis();
        filterChain.doFilter(servletRequest, servletResponse);
        logger.info("执行接口耗时: {}", (System.currentTimeMillis() - start));
    }

}
```
编写一个类，实现Filter接口，实现过滤方法doFilter()，配置自定义Filter的拦截路径，我们在JSP时代是在web.xml中去配置，但是Spring Boot项目并没有web.xml这个文件，我们需要FilterRegistrationBean来完成配置。实现如下：


```java
@Configuration
public class FilterConfig {

    @Bean
    public FilterRegistrationBean registerFilter() {
        FilterRegistrationBean registration = new FilterRegistrationBean();
        // 设置过滤器
        registration.setFilter(new RecordFilter());
        // 配置拦截路径
        registration.addUrlPatterns("/*");
        // 设置过滤器名称
        registration.setName("RecordFilter");
        // 设置执行顺序
        registration.setOrder(1);
        return registration;
    }

}
```
这个和web.xml没什么区别，只是形式不同。当然我们也可以选择基于注解方式进行配置，这个只需在我们刚刚自定义的过滤器类名上面加即可，不需要再新建一个类。这两种方式你可以任意选择一种即可。
​

```java
@Order(1)
@WebFilter(filterName="recordFilter", urlPatterns="/*")
@Configuration
public class RecordFilter implements Filter {

    private static final Logger logger = LoggerFactory.getLogger(RecordFilter.class);

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        logger.info("进入RecordFilter");
        long start = System.currentTimeMillis();
        filterChain.doFilter(servletRequest, servletResponse);
        logger.info("执行接口耗时: {}", (System.currentTimeMillis() - start));
    }

}
```
![image-20211029161527519](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20211029161527519.png)
## 生命周期


Filter生命周期包含一下几个方法
​


1. 构造器方法
1. init初始化方法：Web项目启动时执行
1. doFilter方法：拦截到请求执行
1. destory销毁：Web项目停止时执行

## FilterConfig类

FilterConfig类是过滤器的配置文件类。Tomcat每次创建Filter的时候，也会同时创建一个FilterConfig类，这里包含了Filter配置文件的配置信息。
​

FilterConfig类的作用是获取filter过滤器的配置内容
​


1. 获取Filter的名称
1. 过去Filter中配置的初始化参数
1. 获取ServletContext对象

## FilterChain过滤器链


FilterChain就是过滤器链，多个过滤器一起工作，我们常用的认证授权框架Spring Security就是基于过滤器链实现的。下面演示多个过滤器的执行步骤
​

```java
@Order(1)
@WebFilter(filterName="filterA", urlPatterns="/*")
@Configuration
public class FilterA implements Filter {

    private static final Logger logger = LoggerFactory.getLogger(FilterA.class);

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        logger.info("进入FilterA开始");
        filterChain.doFilter(servletRequest, servletResponse);
        logger.info("进入FilterA结束");
    }

}
```
```java
@Order(2)
@WebFilter(filterName="filterB", urlPatterns="/*")
@Configuration
public class FilterB implements Filter {

    private static final Logger logger = LoggerFactory.getLogger(FilterB.class);

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        logger.info("进入FilterB开始");
        filterChain.doFilter(servletRequest, servletResponse);
        logger.info("进入FilterB结束");
    }

}
```
```java
@RestController
public class TestController {

    private static final Logger logger = LoggerFactory.getLogger(TestController.class);

    @RequestMapping(value = "/test")
    public void test() throws InterruptedException {
        logger.info("访问成功");
    }
}
```
访问日志

![image-20211029161557172](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20211029161557172.png)

由此我们可以看出多个过滤器的工作流程如下：

![image-20211029161624771](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20211029161624771.png)

filterChain.doFilter()方法的作用



1. 如果还存在过滤器，执行下一个过滤器；
1. 如果不存在过滤器，执行目标资源；



多个Filter过滤器执行的特点
​


1. 所有Filter和目标资源都执行在同一个线程中；
1. 多个Filter共同执行的时候，它们都使用同一个request对象；
