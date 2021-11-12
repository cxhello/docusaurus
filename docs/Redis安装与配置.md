### 1.下载

> 下载地址：https://redis.io/download

![image-20200413214119050](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200413214119050.png)

### 2.解压缩

将它拷贝到我们的Linux目录/opt下

![image-20200413214631774](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200413214631774.png)

执行解压命令

`tar -zxvf redis-5.0.8.tar.gz`

解压完成后进入目录

`cd /opt/redis-5.0.8/`

### 3.安装

前提：查看是否安装gcc

`gcc -v`

如未安装gcc执行如下命令进行安装

````bash
yum install gcc
yum install gcc-c++
````

如果在安装gcc之前执行了make编译命令，然后报错了，这个时候就需要先执行一下清理，再去进行编译

`make distclean`

使用make命令进行编译

`make`

使用make install进行安装，路径设置为：/usr/loca/redis 

`make install PREFIX=/usr/local/redis`

进入安装目录

`cd /usr/local/redis/`

该目录下会有一个bin文件夹，进入bin目录

![image-20200413221139911](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200413221139911.png)

我们一般都设置redis后台启动，所以回到redis的解压目录去找redis.conf配置文件，找到之后拷贝到安装目录

````bash
cd /opt/redis-5.0.8/
cp redis.conf /usr/local/redis/bin/
cd /usr/local/redis/bin/
````

![image-20200413221544715](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200413221544715.png)

修改配置文件，修改以下参数

`vim /usr/local/redis/bin/redis.conf`

```bash
# 注释掉只能接受本机访问
#bind 127.0.0.1

# 关闭保护模式
protected-mode no

# 修改启动方式,no表示前台启动,yes表示后台启动
daemonize yes
```

![image-20200413223659669](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200413223659669.png)

![image-20200413223737716](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200413223737716.png)

保存配置文件，然后启动

```bash
cd /usr/local/redis/bin
./redis-server redis.conf
./redis-cli
```

也可以使用redis客户端进行连接

![image-20200413224118086](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200413224118086.png)

### 安装redis 6.0.0 编译错误

![image-20201102110758813](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20201102110758813.png)

原因是自redis 6.0.0 之后，编译redis需要支持C11特性，CentOs7默认gcc版本为4.8.5，C11特性在4.9中引入，需安装Developer Toolset 7使用gcc7编译

`yum install centos-release-scl devtoolset-7`

这不会更新系统原有gcc软件包，在当您需要使用gcc7工具链时，使用下列命令进入环境

`scl enable devtoolset-7 bash`

然后就可以使用make命令开始编译了