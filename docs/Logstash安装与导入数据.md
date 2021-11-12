我在[Elasticsearch安装与配置](article/环境安装/Elasticsearch安装与配置.md)这篇文档已经把Logstash下载好并且拷贝到服务器里了，这里就直接不演示下载了。

### 1.解压缩

```bash
# 解压缩
tar -zxvf logstash-7.1.0.tar.gz
# 更改目录Owner
chown -R es:es /opt/elk/logstash-7.1.0/
```

### 2.启动Logstash

编写logstash配置文件

`vim /opt/elk/logstash-7.1.0/config/logstash.conf`

```
input {
  file {
    path => "/opt/elk/logstash-7.1.0/bin/movies.csv"
    start_position => "beginning"
    sincedb_path => "/dev/null"
  }
}
filter {
  csv {
    separator => ","
    columns => ["id","content","genre"]
  }

  mutate {
    split => { "genre" => "|" }
    remove_field => ["path", "host","@timestamp","message"]
  }

  mutate {

    split => ["content", "("]
    add_field => { "title" => "%{[content][0]}"}
    add_field => { "year" => "%{[content][1]}"}
  }

  mutate {
    convert => {
      "year" => "integer"
    }
    strip => ["title"]
    remove_field => ["path", "host","@timestamp","message","content"]
  }

}
output {
   elasticsearch {
     hosts => "http://192.168.223.137:9200"
     index => "movies"
     document_id => "%{id}"
   }
  stdout {}
}
```

保存配置文件即可，启动logstash之前一定要保证elasticsearch启动

```bash
# 切换用户
su es
# 启动logstash
/opt/elk/logstash-7.1.0/bin/logstash -f /opt/elk/logstash-7.1.0/config/logstash.conf
```

### 3.查看结果

![image-20200621001356076](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200621001356076.png)

logstash已经写入数据到elasticsearch中