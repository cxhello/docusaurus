## 方法一：包管理器安装

在 Linux 上安装 Git 向来仅需⼀⾏命令即可搞定，因为各式各样的包管理器帮了我们⼤忙，所以对于 CentOS 系统来讲，直接执⾏如下命令即可安装：

`yum install git`

当然通过这种⽅式安装的 Git 可能不是较新版的 Git ,不过⼀般来说是够⽤的。

## 方法二：通过源码编译安装

### 1.下载

> 下载地址：https://mirrors.edge.kernel.org/pub/software/scm/git/

![image-20200609203907175](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200609203907175.png)

也可以使用如下命令进行下载，这样就不用再传到Linux服务器上了，省了很多事情。

`wget https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.27.0.tar.gz`

### 2.解压缩

将它拷贝到我们的Linux目录/opt下

![image-20200609204127917](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200609204127917.png)

执行解压命令

`tar -zxvf git-2.27.0.tar.gz`

### 3.安装

卸载CentOS自带的git 1.8.3.1

![image-20200609204647133](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200609204647133.png)

`yum remove git`

提前安装可能需要的依赖

`yum install curl-devel expat-devel gettext-devel openssl-devel zlibdevel gcc-c++ perl-ExtUtils-MakeMaker`

编译安装git

![image-20200609205122395](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200609205122395.png)

```bash
cd git-2.27.0/
make configure
./configure --prefix=/usr/local/git
make profix=/usr/local/git
make install
```

### 4.配置环境变量

`vim /etc/profile`

将下面这两行代码拷贝到文件末尾并保存

```bash
export GIT_HOME=/usr/local/git
export PATH=$PATH:$GIT_HOME/bin
```

![image-20200609210445600](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200609210445600.png)

重载环境变量

`source /etc/profile`

### 5.查看结果

`git --version`

![image-20200609212717093](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200609212717093.png)