### 1.下载

> 下载地址：http://nginx.org/en/download.html

![image-20200414222458676](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200414222458676.png)

### 2.解压缩

将它拷贝到我们的Linux目录/opt下

![image-20200414224805381](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200414224805381.png)

执行解压命令

`tar -zxvf nginx-1.16.1.tar.gz`

解压完成后进入目录

`cd /opt/nginx-1.16.1/`

### 3. 安装

前提：查看系统中是否安装了gcc、pcre-devel、zlib-devel、openssl-devel

如未安装执行如下命令进行安装

`yum -y install gcc pcre-devel zlib-devel openssl openssl-devel`

在nginx目录中执行配置命令

`./configure`

![image-20200414225446805](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200414225446805.png)

出现类似这样的就可以进行安装了。

`make && make install`

启动测试安装是否成功

```bash
# 启动命令
/usr/local/nginx/sbin/nginx
# 关闭命令
/usr/local/nginx/sbin/nginx -s stop
# 重新加载命令
/usr/local/nginx/sbin/nginx -s reload
```

![image-20200414231112930](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200414231112930.png)

设置nginx为自启动服务，修改linux 启动脚本/etc/rc.d/rc，加入/usr/local/nginx/sbin/nginx

`vim /etc/rc.d/rc.local`

![image-20200414230524599](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200414230524599.png)

设置执行权限

`chmod 755 /etc/rc.d/rc.local`

至此nginx安装完成！