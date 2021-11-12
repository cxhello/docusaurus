# Windows安装MySQL

### 1.下载

> 下载地址：https://dev.mysql.com/downloads/

![image-20210130105700196](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210130105700196.png)

这里我们下载的版本是 `MySQL 5.7.32` 的压缩包，不需要安装，但需要配置

![image-20210130105822374](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210130105822374.png)

### 2.安装

将文件解压到要安装的位置，并新建配置文件和数据存放目录

my.ini：配置文件

data：数据存放目录

![image-20210130112251381](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210130112251381.png)

`my.ini` ，内容如下

```ini
[mysql]
 
# 设置mysql客户端默认字符集
default-character-set=utf8 
 
[mysqld]

# 设置3306端口
port=3306 
# 设置mysql的安装目录
basedir=D:\developer_tools\mysql-5.7.32-winx64
# 设置mysql数据库的数据的存放目录
datadir=D:\developer_tools\mysql-5.7.32-winx64\data
# 允许最大连接数
max_connections=200
# 服务端使用的字符集默认为8比特编码的latin1字符集 
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```

### 3.配置环境变量

- 新建变量
  - 变量名：MYSQL_HOME
  - 变量值：MySQL 解压的目录

![image-20210130112540391](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210130112540391.png)

- 添加变量
  - 变量名：Path
  - 变量值：%MYSQL_HOME%\bin

![image-20210130112817285](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210130112817285.png)

### 4.安装MySQL服务

以`管理员`身份运行 cmd，进入 MySQL 的 bin 目录，初始化数据库文档

`mysqld --initialize`

初始化成功后，会在 `datadir` 目录下生成一些文档，其中，`xxx.err` 文档里说明了 root 账户的临时密码。那行大概长这样：

![image-20210130130358850](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210130130358850.png)

```
2021-01-30T05:02:57.671847Z 1 [Note] A temporary password is generated for root@localhost: vGtZnDfEN7_D
```

密码就是：`vGtZnDfEN7_D`

接下来还是在 bin 目录注册 MySQL 服务

`mysqld -install`

![image-20210130130452164](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210130130452164.png)

### 5.启动MySQL服务

`net start mysql`

![image-20210130130512119](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210130130512119.png)

### 6.配置MySQL账户

密码就是刚刚的默认密码

`mysql -u root -p`

![image-20210130130638027](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210130130638027.png)

因为初始化密码默认是过期的，所以查看数据库会报错，必须修改密码之后才能操作数据库。

`ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';`

开启 MySQL 远程访问

```mysql
# 授予通过网络方式登录的的root用户，对所有库所有表的全部权限，密码设为123456
grant all privileges on *.* to root@'%' identified by '123456';
# 所有通过user表的修改，必须用该命令才能生效
flush privileges;
```

测试连接

![image-20210130115823650](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210130115823650.png)

### 7.卸载MySQL

```bash
# 1.停止MySQL服务
net stop mysql
# 2.删除MySQL服务
mysql remove
# 3.清理MySQL数据
# 4.删除系统环境变量
```

