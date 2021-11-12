项目测试环境数据库数据丢失，特此记录一下。当时是用 docker 安装的，以为临时使用一段时间，也没有持久化。突然前天 docker 日志满了，同事想着去清理日志，使用了如下命令：

```bash
docker system prune
```

结果当时 MySQL 容器当时正常处于停止状态，结果容器一下子就被干掉了，我们备份的数据还是三月份的，这下糟糕了。然后各种研究开始恢复。

然后我就去官方文档去研究这个命令是干什么的，上面用到的 `docker system prune` 意思是：

> Remove all unused containers, networks, images (both dangling and unreferenced), and optionally, volumes.
>
> 删除所有未使用的容器、网络、图像（悬空和未引用的图像）以及卷（可选）。
>
> By default, volumes are not removed to prevent important data from being deleted if there is currently no container using the volume. Use the `--volumes` flag when running the command to prune volumes as well:
>
> 默认情况下，如果当前没有使用卷的容器，则不会删除卷以防止删除重要数据。运行命令时也可以使用 `--volumes` 标志来修剪卷：

这下心放下了一半，还好数据卷没有被删除，我们可以利用数据卷可以进行恢复数据。接下来记录下我的恢复方案吧。

### 1.查找数据卷位置

数据卷目录在 `/var/lib/docker/volumes` 下，每个容器都会在该目录下有一个文件夹，如果容器还存在的话，我们可以使用 `docker inspect 容器ID` 去查看 数据卷位置，这下容器被删除了，可怎么办，只能挨个去找了，一般 MySQL 容器数据卷目录下会有一个 `_data` 目录，该目录下会显示你每个数据库的文件夹，最终找到了。

![image-20210510113543085](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210510113543085.png)

![image-20210510114631505](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210510114631505.png)

这个 `cxhello` 就是我们的测试库，现在我们就可以恢复数据了。

### 2.恢复

1. 使用 `docker volume create 数据卷名字` 命令新建一个数据卷，`docker volume ls` 查看数据卷列表

![image-20210510114945865](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210510114945865.png)

> 注意：使用数据卷进行挂载的时候，数据卷必须是一个空的目录，也就是说不能有任何数据。

2. 然后创建容器

```
docker run -d -p 3309:3306 -v mysqldata:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name huifu mysql:5.7
```

![image-20210510115714866](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210510115714866.png)

3. 在恢复数据之前需要把刚刚建立的数据卷里面关联的内容删除掉，然后把之前的数据卷内容复制到现在的数据卷进行数据恢复。

```bash
cd /var/lib/docker/volumes/mysqldata/_data/
rm -f *
rm -f -R *
```

![image-20210510130608341](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210510130608341.png)

4. 复制内容到数据卷

```bash
cd /var/lib/docker/volumes/1db16a9dfdf3442b117ebc2ec11df5df4db717cfd567c77fa0a49905a9652fa0/_data/
cp -R * /var/lib/docker/volumes/mysqldata/_data/
```

![image-20210510131213523](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210510131213523.png)

至此数据库数据恢复完成，进入恢复的容器查看

![image-20210510131728135](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210510131728135.png)

### 参考文章

> https://docs.docker.com/engine/reference/commandline/system_prune/

> https://www.cnblogs.com/cheyunhua/p/13433400.html