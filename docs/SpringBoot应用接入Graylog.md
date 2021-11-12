# Spring Boot 应用接入 Graylog



## docker-compose.yml

```yaml
version: '3'
services:
  # MongoDB: https://hub.docker.com/_/mongo/
  mongo:
    image: mongo:4.2
    networks:
      - graylog
  # Elasticsearch: https://www.elastic.co/guide/en/elasticsearch/reference/7.10/docker.html
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch-oss:7.10.2
    environment:
      - http.host=0.0.0.0
      - transport.host=localhost
      - network.host=0.0.0.0
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    deploy:
      resources:
        limits:
          memory: 1g
    networks:
      - graylog
  # Graylog: https://hub.docker.com/r/graylog/graylog/
  graylog:
    image: graylog/graylog:4.1
    environment:
      # CHANGE ME (must be at least 16 characters)!
      - GRAYLOG_PASSWORD_SECRET=somepasswordpepper
      # Password: admin
      - GRAYLOG_ROOT_PASSWORD_SHA2=8c6976e5b5410415bde908bd4dee15dfb167a9c873fc4bb8a81f6f2ab448a918
      - GRAYLOG_HTTP_EXTERNAL_URI=http://127.0.0.1:9000/
      - GRAYLOG_ROOT_TIMEZONE=Asia/Shanghai
    entrypoint: /usr/bin/tini -- wait-for-it elasticsearch:9200 --  /docker-entrypoint.sh
    networks:
      - graylog
    restart: always
    depends_on:
      - mongo
      - elasticsearch
    ports:
      # Graylog web interface and REST API
      - 9000:9000
      # Syslog TCP
      - 1514:1514
      # Syslog UDP
      - 1514:1514/udp
      # GELF TCP
      - 12201:12201
      # GELF UDP
      - 12201:12201/udp
networks:
  graylog:
    driver: bridge
```

使用命令 `docker-compose up`启动即可



## Graylog 配置

`http://192.168.9.3:9000`

`默认账号密码都是admin`

![image-20210928135036612](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210928135036612.png)

`点击 System -> Inputs`

![image-20210928135100298](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210928135100298.png)

`搜索 UDP，选择 GELF UDP，然后在点击 Launch new input`

![image-20210928133744340](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210928133744340.png)

![image-20210928133815684](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210928133815684.png)

`勾选 Global，Title 自己随便写一个就好，其他项默认，然后点击保存即可。`

![image-20210928133917269](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210928133917269.png)



## 项目配置



### pom.xml

```xml
<dependency>
	<groupId>de.siegmar</groupId>
	<artifactId>logback-gelf</artifactId>
	<version>3.0.0</version>
</dependency>
```

### logback.xml

```xml
<appender name="GELF" class="de.siegmar.logbackgelf.GelfUdpAppender">
	<graylogHost>192.168.9.3</graylogHost>
	<graylogPort>12201</graylogPort>
	<maxChunkSize>508</maxChunkSize>
	<useCompression>true</useCompression>
	<messageIdSupplier class="de.siegmar.logbackgelf.MessageIdSupplier"/>
	<encoder class="de.siegmar.logbackgelf.GelfEncoder">
<!--    <originHost>localhost</originHost>-->
		<includeRawMessage>false</includeRawMessage>
		<includeMarker>true</includeMarker>
		<includeMdcData>true</includeMdcData>
		<includeCallerData>false</includeCallerData>
		<includeRootCauseData>false</includeRootCauseData>
		<includeLevelName>true</includeLevelName>
		<shortPatternLayout class="ch.qos.logback.classic.PatternLayout">
			<pattern>%d - %m%nopex</pattern>
		</shortPatternLayout>
		<fullPatternLayout class="ch.qos.logback.classic.PatternLayout">
			<pattern>%d - %m%n</pattern>
		</fullPatternLayout>
		<numbersAsString>false</numbersAsString>
		<staticField>app_name:graylog-test</staticField>
		<staticField>os_arch:${os.arch}</staticField>
		<staticField>os_name:${os.name}</staticField>
		<staticField>os_version:${os.version}</staticField>
	</encoder>
</appender>

<root level="info">
	<appender-ref ref="GELF"/>
</root>
```

启动项目进行测试。



## 查看日志

![image-20210928134940663](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210928134940663.png)