###### CentOs7安装docker

```bash
/var/run/yum.pid 已被锁定 解决办法
rm -f /var/run/yum.pid
1.卸载旧版本
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
2.添加docker-ce yum源
//扩展yum功能
yum install -y yum-utils
//添加软件源信息
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo     
//自动选择最快yum仓库源
yum makecache fast
3.查看当前可以安装docker-ce版本
yum list docker-ce --showduplicates | sort -r
yum -y install docker-ce-[VERSION]    //安装指定版本的格式 ,注意3:xxx 请移除3:
yum install -y docker-ce-19.03.5-3.el7
4.启动docker
systemctl start docker
docker info
docker version
5.通过运行hello-world 映像来验证是否正确安装了Docker Engine-Community
docker run hello-world
6.卸载docker
yum remove docker-ce
主机上的映像，容器，卷或自定义配置文件不会自动删除。要删除所有图像，容器和卷
sudo rm -rf /var/lib/docker
```

```bash
搜索镜像
docker search 名字
```

```bash
下载镜像
docker pull 名字
```

```bash
创建容器
docker run -d --name=docker-centos2 centos /bin/sh -c "while true;do echo hello caixiaohui;sleep 2;done"
docker run -i -t --name=c1 docker.io/centos /bin/bash 
docker run -i -t -d --name=c1 docker.io/centos /bin/bash
docker run -itd --name=c1 docker.io/centos /bin/bash
-t: 在新容器内指定一个伪终端或终端。
-i: 允许你对容器内的标准输入 (STDIN) 进行交互。
-d: 让容器在后台运行。
-p: 自定义映射 hostPort:containerPort。
-P: 将容器内部使用的网络端口映射到我们使用的主机上。随机映射
加了 -d 参数默认不会进入容器，想要进入容器需要使用指令 docker exec
```

```bash
退出并后台运行：
ctrl+p+q  ||  exit
```

```bash
进入容器
docker attach 容器ID
docker exec -it 容器ID /bin/bash
推荐使用 docker exec 命令，因为exec命令退出容器终端，不会导致容器的停止。
```

```bash
查看正在运行的容器
docker ps
查看所有容器
docker ps -a
```

```bash
启动容器
docker start c1
```

```bash
停止容器
docker stop c1
```

```bash
删除容器
docker rm c1
```

```bash
容器持久化保存
docker run -tid --name=c1 -v /data/:/tmp/data docker.io/centos /bin/bash 
echo "c2 docker test" > /var/www/html/index.html
touch /tmp/{1..10}.jpg
```

```bash
容器开机启动
--restart=always
```

```bash
docker安装MySQL
docker pull mysql:5.5
docker run -itd -P -e MYSQL_ROOT_PASSWORD=123456 mysql:5.5
docker ps
docker exec -it 168ae768ba20 /bin/bash
```

