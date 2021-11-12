### 1.下载

> 下载地址：http://kafka.apache.org/downloads

![image-20200518220542906](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200518220542906.png)

### 2.解压缩

将它拷贝到我们的Linux目录/opt下

![image-20200518220903254](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200518220903254.png)

执行解压命令

`tar -zxvf kafka_2.11-0.11.0.0.tgz`

### 3.安装

创建kafka数据文件夹

`mkdir /opt/kafka_2.11-0.11.0.0/data`

修改kafka配置文件

`vim /opt/kafka_2.11-0.11.0.0/config/server.properties`

![image-20200518223755955](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200518223755955.png)

![image-20200518223819084](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200518223819084.png)

启动zookeeper

`/opt/apache-zookeeper-3.6.1-bin/bin/zkServer.sh start`

进入kafka的bin目录

`cd /opt/kafka_2.11-0.11.0.0/bin/`

![image-20200518224903052](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200518224903052.png)

```bash
# 启动命令
./kafka-server-start.sh -daemon /opt/kafka_2.11-0.11.0.0/config/server.properties
# 关闭命令
./kafka-server-stop.sh /opt/kafka_2.11-0.11.0.0/config/server.properties
# 创建topic
./kafka-topics.sh --create --zookeeper 192.168.223.137:2181 --topic first --partitions 1 --replication-factor 1
# 查看topic
./kafka-topics.sh --list --zookeeper 192.168.223.137:2181
# 删除topic
./kafka-topics.sh --delete --zookeeper 192.168.223.137:2181 --topic first
# 查看topic描述
./kafka-topics.sh --describe --zookeeper 192.168.223.137:2181 --topic first
```

![image-20200518231037309](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200518231037309.png)