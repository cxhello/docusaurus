### 1.下载

> 下载地址：https://nodejs.org/zh-cn/download/

![image-20210509120210615](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210509120210615.png)

点击右键复制链接地址，然后使用命令

```bash
wget https://nodejs.org/dist/v14.16.1/node-v14.16.1-linux-x64.tar.xz
```

![image-20210509120441916](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210509120441916.png)

### 2.解压缩

执行解压命令

```bash
tar -xvf node-v14.16.1-linux-x64.tar.xz
```

### 3.安装

通过建立软链接的方式设置为全局

```bash
ln -s /opt/node-v14.16.1-linux-x64/bin/node /usr/local/bin/
ln -s /opt/node-v14.16.1-linux-x64/bin/npm /usr/local/bin/
```

![image-20210509120954549](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210509120954549.png)

### 4.查看版本

```bash
node -v
npm -v
```

![image-20210509121058636](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210509121058636.png)