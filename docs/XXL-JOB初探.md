# XXL-JOB初探

相信大家对任务调度都不陌生，说的通熟一点就是定时任务；这个在我们的项目中或多或少都存在，我们可以用 JDK 自带的（Timer、ScheduledExecutor）来实现，也可以用 Spring 的 Scheduler 来实现，不管用以上哪种方式，我们都是在单机上跑，如果我们以集群的方式部署，会不会出现什么问题 ？

集群中的各个节点都会执行定时调度，会有重复执行的问题，那怎么办？ 我们可以加配置，只启动某个节点的定时任务，但是这时候又会出现单点问题

那有没有什么办法，既能避免重复执行，又不会出现单点问题呢？ 分布式调度应运而生，常见的分布式任务调度框架有：quartz 、cronsun、Elastic-job、saturn、lts、TBSchedule、xxl-job 等。

今天我们就一起来了解下另外一个分布式调度平台：XXL-JOB

### 1.部署调度中心

> 项目源码下载地址：https://github.com/xuxueli/xxl-job/

#### 1.1 初始化“调度数据库”

将项目源码克隆下来，获取 “调度数据库初始化SQL脚本” 并执行即可。

“调度数据库初始化SQL脚本” 位置为：

`/xxl-job/doc/db/tables_xxl_job.sql`

#### 1.2 调度中心配置

调度中心配置文件地址：

`/xxl-job/xxl-job-admin/src/main/resources/application.properties`

如没有其他需求，只修改数据库相关配置即可

![image-20210206204951324](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210206204951324.png)

#### 1.3 部署项目

如果已经正确进行上述配置，可将项目编译打包部署。

调度中心访问地址：http://192.168.223.139:8080/xxl-job-admin (该地址执行器将会使用到，作为回调地址)

默认登录账号 “admin/123456”, 登录后运行界面如下图所示。

![image-20210206210321758](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210206210321758.png)

至此“调度中心”项目已经部署成功。

### 2.部署执行器项目

#### 2.1 maven依赖

在自己或公司的项目 `pom` 文件中引入 `xxl-job-core` 的依赖

```xml
<!-- http://repo1.maven.org/maven2/com/xuxueli/xxl-job-core/ -->
<dependency>
    <groupId>com.xuxueli</groupId>
    <artifactId>xxl-job-core</artifactId>
    <version>${最新稳定版本}</version>
</dependency>
```

#### 2.2 执行器项目配置

```properties
### 调度中心部署跟地址 [选填]：如调度中心集群部署存在多个地址则用逗号分隔。执行器将会使用该地址进行"执行器心跳注册"和"任务结果回调"；为空则关闭自动注册；
xxl.job.admin.addresses=http://192.168.223.139:8080/xxl-job-admin
### 执行器通讯TOKEN [选填]：非空时启用；
xxl.job.accessToken=
### 执行器AppName [选填]：执行器心跳注册分组依据；为空则关闭自动注册
xxl.job.executor.appname=xxl-job-example
### 执行器注册 [选填]：优先使用该配置作为注册地址，为空时使用内嵌服务 ”IP:PORT“ 作为注册地址。从而更灵活的支持容器类型执行器动态IP和动态映射端口问题。
xxl.job.executor.address=
### 执行器IP [选填]：默认为空表示自动获取IP，多网卡时可手动设置指定IP，该IP不会绑定Host仅作为通讯实用；地址信息用于 "执行器注册" 和 "调度中心请求并触发任务"；
xxl.job.executor.ip=
### 执行器端口号 [选填]：小于等于0则自动获取；默认端口为9999，单机部署多个执行器时，注意要配置不同执行器端口；
xxl.job.executor.port=9999
### 执行器运行日志文件存储磁盘路径 [选填] ：需要对该路径拥有读写权限；为空则使用默认路径；
xxl.job.executor.logpath=/data/applogs/xxl-job/jobhandler
### 执行器日志文件保存天数 [选填] ： 过期日志自动清理, 限制值大于等于3时生效; 否则, 如-1, 关闭自动清理功能；
xxl.job.executor.logretentiondays=30
```

![image-20210206211900382](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210206211900382.png)

#### 2.3 执行器组件配置

```java
package com.cxhello.example.config;

import com.xxl.job.core.executor.impl.XxlJobSpringExecutor;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @author cxhello
 * @create 2021/2/6
 */
@Configuration
public class XxlJobConfig {

    private Logger logger = LoggerFactory.getLogger(XxlJobConfig.class);

    @Value("${xxl.job.admin.addresses}")
    private String adminAddresses;

    @Value("${xxl.job.accessToken}")
    private String accessToken;

    @Value("${xxl.job.executor.appname}")
    private String appname;

    @Value("${xxl.job.executor.address}")
    private String address;

    @Value("${xxl.job.executor.ip}")
    private String ip;

    @Value("${xxl.job.executor.port}")
    private int port;

    @Value("${xxl.job.executor.logpath}")
    private String logPath;

    @Value("${xxl.job.executor.logretentiondays}")
    private int logRetentionDays;


    @Bean
    public XxlJobSpringExecutor xxlJobExecutor() {
        logger.info(">>>>>>>>>>> xxl-job config init.");
        XxlJobSpringExecutor xxlJobSpringExecutor = new XxlJobSpringExecutor();
        xxlJobSpringExecutor.setAdminAddresses(adminAddresses);
        xxlJobSpringExecutor.setAppname(appname);
        xxlJobSpringExecutor.setAddress(address);
        xxlJobSpringExecutor.setIp(ip);
        xxlJobSpringExecutor.setPort(port);
        xxlJobSpringExecutor.setAccessToken(accessToken);
        xxlJobSpringExecutor.setLogPath(logPath);
        xxlJobSpringExecutor.setLogRetentionDays(logRetentionDays);

        return xxlJobSpringExecutor;
    }

    /**
     * 针对多网卡、容器内部署等情况，可借助 "spring-cloud-commons" 提供的 "InetUtils" 组件灵活定制注册IP；
     *
     *      1、引入依赖：
     *          <dependency>
     *             <groupId>org.springframework.cloud</groupId>
     *             <artifactId>spring-cloud-commons</artifactId>
     *             <version>${version}</version>
     *         </dependency>
     *
     *      2、配置文件，或者容器启动变量
     *          spring.cloud.inetutils.preferred-networks: 'xxx.xxx.xxx.'
     *
     *      3、获取IP
     *          String ip_ = inetUtils.findFirstNonLoopbackHostInfo().getIpAddress();
     */

}
```

#### 2.4 开发定时任务

```java
package com.cxhello.example.jobhandler;

import com.xxl.job.core.handler.annotation.XxlJob;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

/**
 * @author cxhello
 * @create 2021/2/6
 */
@Component
public class ExampleXxlJob {

    private static Logger logger = LoggerFactory.getLogger(ExampleXxlJob.class);

    @Value("${server.port}")
    private int port;

    @XxlJob("testXxlJob")
    public void testXxlJob() {
        logger.info("XXL-JOB, Hello World, port: {}.", port);
    }

}
```

如果已经正确进行上述配置，可将项目编译打包部署。

#### 2.5 新建执行器

在任务调度中心-执行器管理-新建执行器，`AppName` 为执行器项目配置文件中的 `AppName`，名称填项目名字，注册方式选择自动注册即可。

![image-20210206215723245](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210206215723245.png)

#### 2.6 新建任务

在任务调度中心-任务管理-新增任务，

1. 执行器选择刚刚新建的执行器；
2. 任务描述写这个任务用来做什么，负责人写编写此代码的人即可，
3. 调度类型选择 `CRON`；
4. Cron 根据项目需求去填写 Cron 表达式，
5. 运行模式选择Bean；
6. JobHandler 填写代码中 @XxlJob 注解中的自定义 jobhandler名称；

其他如果没有特殊需求默认即可，然后保存完，点启动即可。

![image-20210206220503013](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210206220503013.png)

现在我们可以去执行器项目中查看一下日志，看定时任务是否在执行

![image-20210206223327912](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210206223327912.png)

发现已经在执行了。

### 3.调度中心集群

调用中心集群搭建非常简单，多个项目的DB保持一致，集群时钟保持一致，到时候可以使用 nginx 对多个调用中心做负载均衡。

### 4.执行器集群

我们项目如果在集群化部署的情况下可以选择配置路由策略，调度中心会根据路由策略将调度请求发送给具体的一个执行器，我们选择轮询进行测试。路由策略包括：

```
- FIRST（第一个）：固定选择第一个机器；
- LAST（最后一个）：固定选择最后一个机器；
- ROUND（轮询）：；
- RANDOM（随机）：随机选择在线的机器；
- CONSISTENT_HASH（一致性HASH）：每个任务按照Hash算法固定选择某一台机器，且所有任务均匀散列在不同机器上。
- LEAST_FREQUENTLY_USED（最不经常使用）：使用频率最低的机器优先被选举；
- LEAST_RECENTLY_USED（最近最久未使用）：最久未使用的机器优先被选举；
- FAILOVER（故障转移）：按照顺序依次进行心跳检测，第一个心跳检测成功的机器选定为目标执行器并发起调度；
- BUSYOVER（忙碌转移）：按照顺序依次进行空闲检测，第一个空闲检测成功的机器选定为目标执行器并发起调度；
- SHARDING_BROADCAST(分片广播)：广播触发对应集群中所有机器执行一次任务，同时系统自动传递分片参数；可根据分片参数开发分片任务；
```

搭建非常简单，只需要注意两点

1. 执行器回调地址（xxl.job.admin.addresses）需要保持一致；执行器根据该配置进行执行器自动注册等操作；
2. 同一个执行器集群内AppName（xxl.job.executor.appname）需要保持一致；调度中心根据该配置动态发现不同集群的在线执行器列表。

![image-20210208222428340](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210208222428340.png)

由于我是在一台机器上搭建，所以项目端口，和执行器端口都要唯一，修改完后，可将项目编译打包部署。启动之后，去调度中心修改路由策略为轮训，再启动任务调度，然后就可以去查看调度日志了。

![image-20210208223048306](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210208223048306.png)

![image-20210208223203013](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210208223203013.png)

![image-20210208223258397](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210208223258397.png)

这样执行器集群就搭建成功了。

`如果你项目中的定时任务有指定集群中的某台机器去执行的需求，可以为该任务绑定一个单独的执行器（执行器支持复用AppName），该执行器手动指定固定的IP地址，我也是从官方 issues 上看到的此方案。`

> https://github.com/xuxueli/xxl-job/issues/216

![image-20210208225241116](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210208225241116.png)

![image-20210208225457655](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210208225457655.png)

![image-20210208225616391](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210208225616391.png)

现在我们就看到只有 8081 端口的执行器在执行任务，这样就可以指定执行器了。

> 执行器示例代码地址：https://github.com/cxhello/xxl-job-example

### 参考文章

> https://www.xuxueli.com/xxl-job/