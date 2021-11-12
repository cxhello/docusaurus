### 1.下载

> 下载地址：https://www.oracle.com/java/technologies/javase/javase8u211-later-archive-downloads.html

![image-20200712170726568](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200712170726568.png)

### 2.解压缩

将它拷贝到我们的Linux目录/opt下

![image-20200712170837822](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200712170837822.png)

执行解压命令

`tar -zxvf jdk-8u241-linux-x64.tar.gz`

### 3.安装

如果系统⾃带有 OpenJDK ，可以按照如下步骤提前卸载。

⾸先查找已经安装的 OpenJDK 包：

`rpm -qa | grep java`

![image-20200712171056669](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200712171056669.png)

接下来可以将 java 开头且带有openjdk的安装包均卸载即可：

```bash
yum -y remove java-1.7.0-openjdk-1.7.0.191-2.6.15.5.el7.x86_64
yum -y remove java-1.7.0-openjdk-headless-1.7.0.191-2.6.15.5.el7.x86_64
yum -y remove java-1.8.0-openjdk-headless-1.8.0.181-7.b13.el7.x86_64
yum -y remove java-1.8.0-openjdk-1.8.0.181-7.b13.el7.x86_64
```

配置环境变量

`vim /etc/profile`

将下面这四行代码拷贝到文件末尾并保存

```bash
JAVA_HOME=/opt/jdk1.8.0_241
PATH=$PATH:$JAVA_HOME/bin
export JAVA_HOME PATH
export CLASSPATH=.:JAVA_HOME/lib/dt.jar:JAVA_HOME/lib/tools.jar
```

重载环境变量

`source /etc/profile`

### 4.查看结果

```bash
java -version
javac
```

![image-20200712172313248](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200712172313248.png)