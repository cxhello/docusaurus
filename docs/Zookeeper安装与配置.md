### 1.下载

> 下载地址：https://zookeeper.apache.org/releases.html

![image-20200517224033981](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200517224033981.png)

### 2.解压缩

将它拷贝到我们的Linux目录/opt下

![image-20200517224354051](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200517224354051.png)

执行解压命令

`tar -zxvf apache-zookeeper-3.6.1-bin.tar.gz`

### 3.安装

创建zookeeper专属目录

`mkdir /zookeeper`

将解压的文件夹拷贝到/zookeeper目录内

`cp -r /opt/apache-zookeeper-3.6.1-bin /zookeeper/`

![image-20200517224909012](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200517224909012.png)

进入conf文件夹，拷贝`zoo_sample.cfg`改为`zoo.cfg`

`cp zoo_sample.cfg zoo.cfg`

![image-20200517225128434](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200517225128434.png)

```bash
# 启动服务命令
./zkServer.sh start
# 关闭服务命令
./zkServer.sh stop
# 客户端连接命令
./zkCli.sh
# 退出
quit
```

![image-20200517230023958](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200517230023958.png)

![image-20200517230237745](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200517230237745.png)

查看zookeeper服务器上的存储信息

![image-20200517230703514](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200517230703514.png)