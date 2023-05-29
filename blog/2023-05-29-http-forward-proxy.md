---
title: Http forward proxy
description: This is my first post on Docusaurus 2. 
---

### 需求
![petter-lagson-8yy05SPf3rQ-unsplash.jpg](./img/petter-lagson-8yy05SPf3rQ-unsplash.jpg)

部署在客户服务器的系统访问外部网络|互联网时，需要通过固定的一个内网服务器+端口代理访问。之前没有遇到过这样的情况，特整理方案如下：

<!--truncate-->

### 代码修改

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

/**
 * @author cxhello
 * @date 2023-05-24
 */
@Component
public class ProxyConfig implements CommandLineRunner {

    private static final Logger logger = LoggerFactory.getLogger(ProxyConfig.class);

    @Value("${proxy.switch:false}")
    private boolean proxySwitch;

    @Value("${proxy.http.proxyHost:192.168.6.5}")
    private String httpProxyHost;

    @Value("${proxy.http.proxyPort:3088}")
    private String httpProxyPort;

    @Value("${proxy.https.proxyHost:192.169.6.5}")
    private String httpsProxyHost;

    @Value("${proxy.https.proxyPort:3088}")
    private String httpsProxyPort;

    @Value("${proxy.http.nonProxyHosts:localhost|127.0.0.1|172.18.0.*}")
    private String httpNonProxyHosts;

    @Value("${proxy.https.nonProxyHosts:localhost|127.0.0.1|172.18.0.*}")
    private String httpsNonProxyHosts;

    @Override
    public void run(String... args) throws Exception {
        if (proxySwitch) {
            logger.info("开始初始化代理");
            System.setProperty("http.proxyHost", httpProxyHost);
            System.setProperty("http.proxyPort", httpProxyPort);
            System.setProperty("https.proxyHost", httpsProxyHost);
            System.setProperty("https.proxyPort", httpsProxyPort);
            System.setProperty("http.nonProxyHosts", httpNonProxyHosts);
            System.setProperty("https.nonProxyHosts", httpsNonProxyHosts);
        }
    }

}
```

### 增加Nacos配置

```yaml
proxy:
  switch: true
  http:
    proxyHost: 192.168.6.5
    proxyPort: 3088
    nonProxyHosts: localhost|127.0.0.1|172.18.0.*|nacos
  https:
    proxyHost: 192.168.6.5
    proxyPort: 3088
    nonProxyHosts: localhost|127.0.0.1|172.18.0.*|nacos
```
