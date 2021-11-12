### 1.下载

> 下载地址：https://dev.mysql.com/downloads/

![image-20200419202803064](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200419202803064.png)

这里我们使用wget下载工具进行下载，直接在linux系统（基于CentOS7）的命令行中执行

`wget -i -c https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm`

![image-20200419210101672](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200419210101672.png)

### 2.安装

```bash
# repo的安装，执行完成后会在/etc/yum.repos.d/目录下生成两个repo文件mysql-community.repo mysql-community-source.repo
rpm -ivh mysql57-community-release-el7-9.noarch.rpm
cd /etc/yum.repos.d/
# 注意：必须进入到/etc/yum.repos.d/目录后再使用yum命令进行安装
yum -y install mysql-server
```



![image-20200419211257879](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200419211257879.png)

查询MySQL版本

`mysqladmin --version`

![image-20200419211649477](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200419211649477.png)

### 3.配置

首先启动MySQL服务

![image-20200419212130818](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200419212130818.png)

```bash
# 查看MySQL服务状态
systemctl status mysqld.service
# 启动MySQL服务
systemctl start mysqld.service
# 停止MySQL服务
systemctl stop mysqld.service
# 重新启动MySQL服务
systemctl restart mysqld.service
```

此时MySQL已经开始正常运行，不过要想进入MySQL还得先找出此时root用户的密码，通过如下命令可以在日志文件中找出密码：

`grep "password" /var/log/mysqld.log`

![image-20200419212801814](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200419212801814.png)

首次登陆通过 mysql -uroot -p进行登录，在Enter password：录入初始化密码进入MySQL数据库

`mysql -uroot -p`

![image-20200419213045685](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200419213045685.png)

因为初始化密码默认是过期的，所以查看数据库会报错，必须修改密码之后才能操作数据库。（注意:密码设置必须要大小写字母数字和特殊符号（,/';:等）,不然不能配置成功）

```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'aBc123..';
```

设置自启动

```bash
# 查看mysql是否自启动（默认自启动）
systemctl list-unit-files|grep mysqld.service 
# 如不是enabled可以运行如下命令设置自启动
systemctl enable mysqld.sercice
```

修改字符集

`  vim /etc/my.cnf`

在最后加上中文字符集配置character_set_server=utf8，修改完重新启动MySQL

![image-20200419214427165](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200419214427165.png)

### 4.开启Mysql远程访问

```sql
# 授予通过网络方式登录的的root用户，对所有库所有表的全部权限，密码设为aBc123..
grant all privileges on *.* to root@'%' identified by 'aBc123..';
# 所有通过user表的修改，必须用该命令才能生效
flush privileges;
```

测试连接

![image-20200419220036206](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200419220036206.png)