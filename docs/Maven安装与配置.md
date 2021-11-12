### 1.下载

> 下载地址：http://maven.apache.org/download.cgi

![image-20200521211903954](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200521211903954.png)

### 2.解压缩

将它拷贝到我们的Linux目录/opt下

![image-20200521212246737](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200521212246737.png)

执行解压命令

`tar -zxvf apache-maven-3.6.3-bin.tar.gz`

### 3.安装

修改maven配置文件

`vim /opt/apache-maven-3.6.3/conf/settings.xml`

首先修改jar包下载位置

```xml
<localRepository>/opt/apache-maven-3.6.3/repository</localRepository>
```

![image-20200521213442321](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200521213442321.png)

然后替换maven源，替换为阿里云的源，找到<mirrors></mirrors>标签对，添加如下代码：

```xml
<mirror>
     <id>alimaven</id>
     <name>aliyun maven</name>
     <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
     <mirrorOf>central</mirrorOf>
</mirror>
```

![image-20200521213701133](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200521213701133.png)

然后指定JDK版本

```xml
<profile>    
     <id>jdk-1.8</id>    
     <activation>    
       <activeByDefault>true</activeByDefault>    
       <jdk>1.8</jdk>    
     </activation>    
       <properties>    
         <maven.compiler.source>1.8</maven.compiler.source>    
         <maven.compiler.target>1.8</maven.compiler.target>    
         <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>    
       </properties>    
</profile>
```

![image-20200521213922027](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200521213922027.png)

保存配置文件即可

### 4.配置

配置环境变量

`vim /etc/profile`

将下面这两行代码拷贝到文件末尾并保存

```bash
MAVEN_HOME=/opt/apache-maven-3.6.3
export PATH=${MAVEN_HOME}/bin:${PATH}
```

![image-20200521214741547](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200521214741547.png)

重载环境变量

`source /etc/profile`

### 5.查看结果

`mvn –v`

![image-20200521215152030](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200521215152030.png)