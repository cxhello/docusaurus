### 1.下载

> 下载地址：https://www.elastic.co/cn/downloads/elasticsearch
>
> 由于官网下载慢，我在华为开源镜像站也找到了下载链接，而且全版本都有
>
> 华为开源镜像站下载地址：https://mirrors.huaweicloud.com/elasticsearch/

### 2.解压缩

将它拷贝到我们的Linux目录/opt/elk下

![image-20200620220005774](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200620220005774.png)

执行解压命令

`tar -zxvf elasticsearch-7.1.0-linux-x86_64.tar.gz`

### 3.修改Linux配置

由于Elasticsearch、Logstash、Kibana均不能以root账号运行。但是Linux对非root账号可并发操作的文件、线程都有限制。所以，部署ELK相关的机器都要调整：

- 修改文件限制

```bash
# 修改系统文件
vim /etc/security/limits.conf
 
# 增加的内容
 
* soft nofile 65536
* hard nofile 65536
* soft nproc 2048
* hard nproc 4096
```

+ 调整虚拟内存&最大并发连接

```bash
# 修改系统文件
vim /etc/sysctl.conf
 
# 增加的内容
vm.max_map_count=655360
fs.file-max=655360

# 使配置文件生效
sysctl -p
```

### 4.启动Eelasticsearch

修改elasticsearch配置文件

`vim /opt/elk/elasticsearch-7.1.0/config/elasticsearch.yml`

```yaml
# 集群名称
cluster.name: my-cluster
# 单个节点名称
node.name: node-1
# 关闭bootstrap自检程序,第二行需添加
bootstrap.memory_lock: false
bootstrap.system_call_filter: false
# 改为当前的ip地址
network.host: 192.168.223.137
# 指定主节点
cluster.initial_master_nodes: ["node-1"]
```

保存配置文件即可

```bash
# 添加es用户
useradd es
# 更改目录Owner
chown -R es:es /opt/elk/elasticsearch-7.1.0/
# 切换用户
su es
# 启动elasticsearch
/opt/elk/elasticsearch-7.1.0/bin/elasticsearch
```

### 5.查看结果

![image-20200620225151308](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200620225151308.png)

```bash
# 安装插件
/opt/elk/elasticsearch-7.1.0/bin/elasticsearch-plugin install analysis-icu
# 查看已安装的插件
/opt/elk/elasticsearch-7.1.0/bin/elasticsearch-plugin list
```

> 访问：http://192.168.223.137:9200/_cat/plugins

![image-20200620230900281](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200620230900281.png)