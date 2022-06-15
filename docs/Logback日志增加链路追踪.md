# Logback日志增加链路追踪

我们一般查找日志会根据自己当时在业务代码中打印的关键字去寻找，有时候日志多了，找起来会比较麻烦，所以可以在每一个请求返回一个链路追踪ID，这样查找日志会比较方便一些。

## 增加配置

```java
import org.slf4j.MDC;
import org.springframework.lang.Nullable;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.UUID;

/**
 * @author cxhello
 * @date 2022/6/14
 */
@Component
public class LogbackInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        MDC.put("traceId", UUID.randomUUID().toString());
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {

    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception {

    }

}
```

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;

import javax.annotation.Resource;

/**
 * @author cxhello
 * @date 2022/6/14
 */
@Configuration
public class WebConfiguration extends WebMvcConfigurationSupport {

    @Resource
    private LogbackInterceptor logbackInterceptor;

    @Override
    protected void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(logbackInterceptor);
    }

}
```

logback配置文件，修改pattern，在中间添加 `%X{traceId}`，表示输出日志时会从MDC（ThreadLocal）中获取当前线程的traceId属性，因为traceId是自定义的所以在拦截中增加了traceId。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="false" scan="true" scanPeriod="1 seconds">

    <conversionRule conversionWord="clr" converterClass="org.springframework.boot.logging.logback.ColorConverter"/>
    <conversionRule conversionWord="wex" converterClass="org.springframework.boot.logging.logback.WhitespaceThrowableProxyConverter"/>
    <conversionRule conversionWord="wEx" converterClass="org.springframework.boot.logging.logback.ExtendedWhitespaceThrowableProxyConverter"/>
    <property name="log.path" value="/opt/logs"/>
    <!--  应用名称  -->
    <property name="log.name" value="bank"/>
    <property name="log.pattern" value="%clr(%d{${LOG_DATEFORMAT_PATTERN:-yyyy-MM-dd HH:mm:ss.SSS}}){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(---){faint} %clr([%15.15t]){faint} %X{traceId} %clr(%-40.40logger{39}){cyan} %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}"/>

    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>${log.pattern}</pattern>
        </encoder>
    </appender>

    <appender name="file" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${log.path}/${log.name}.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log.path}/%d{yyyy-MM-dd}/${log.name}-%d{yyyy-MM-dd}.log.gz</fileNamePattern>
        </rollingPolicy>
        <encoder>
            <pattern>%date %level [%thread] %X{traceId} %logger{36} [%file : %line] %msg%n</pattern>
        </encoder>
    </appender>

    <appender name="file_info" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${log.path}/${log.name}-info.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log.path}/%d{yyyy-MM-dd}/${log.name}-info-%d{yyyy-MM-dd}.log.gz</fileNamePattern>
        </rollingPolicy>
        <encoder>
            <pattern>%date %level [%thread] %X{traceId} %logger{36} [%file : %line] %msg%n</pattern>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>INFO</level>
            <onMatch>ACCEPT</onMatch>32
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <appender name="file_error" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${log.path}/${log.name}-error.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log.path}/%d{yyyy-MM-dd}/${log.name}-error-%d{yyyy-MM-dd}.log.gz</fileNamePattern>
        </rollingPolicy>
        <encoder>
            <pattern>%date %level [%thread] %X{traceId} %logger{36} [%file : %line] %msg%n</pattern>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>ERROR</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <root level="info">
        <appender-ref ref="console"/>
        <appender-ref ref="file"/>
        <appender-ref ref="file_info"/>
        <appender-ref ref="file_error"/>
    </root>

</configuration>
```

![image-20220614192129658](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20220614192129658.png)

## 编写测试代码

![image-20220614192939559](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20220614192939559.png)

我们可以使用`MDC.get("traceId")`方法将traceId封装到统一的返回结果中，类似于下面这样，然后根据traceId去查找相关日志排查问题。

```json
{
    "code":200,
    "msg":"success",
    "traceId":"21c340ea-9f22-4da0-b2aa-69e7d8160f10"
}
```

## 参考文章

> https://blog.csdn.net/weixin_43723635/article/details/107201479