我在[Elasticsearch安装与配置](article/环境安装/Elasticsearch安装与配置.md)这篇文档已经把Kibana下载好并且拷贝到服务器里了，这里就直接不演示下载了。

### 1.解压缩

```bash
# 解压缩
tar -zxvf kibana-7.1.0-linux-x86_64.tar.gz
# 更改目录Owner
chown -R es:es /opt/elk/kibana-7.1.0-linux-x86_64/
```

### 2.启动Kibana

修改Kibana配置文件

`vim /opt/elk/kibana-7.1.0-linux-x86_64/config/kibana.yml`

```yaml
server.host: "0.0.0.0"
elasticsearch.hosts: ["http://192.168.223.137:9200"]
i18n.locale: "zh-CN"
```

保存配置文件即可，启动kibana之前一定要保证elasticsearch启动

```bash
# 切换用户
su es
# 启动kibana
/opt/elk/kibana-7.1.0-linux-x86_64/bin/kibana
```

### 3.查看结果

> 访问：http://192.168.223.137:5601/

![image-20200620233834744](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200620233834744.png)