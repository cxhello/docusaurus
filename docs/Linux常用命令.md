### 一、基础命令

```bash
java -jar java.jar
控制台运行jar包
```

```bash
nohup java -jar java.jar > java.log 2>&1 &
后台运行jar包,表示不挂断运行命令,当账户退出或终端关闭时,程序仍然运行，并且该作业的所有输出被重定向到java.log的文件中。“ > java.log ” 该命令就是指定日志输出的文件。
```

```bash
ps -ef | grep 进程名
ps -ef | grep 进程名 | grep -v grep
netstat -anp | grep 端口号
lsof -i:端口号
查看进程
```

```bash
netstat -lntup
一般查看端口是否占用
netstat命令是一个监控TCP/IP网络的非常有用的工具，它可以显示路由表、实际的网络连接以及每一个网络接口设备的状态信息。
```

```bash
kill -9 进程号
根据进程号强制杀死进程
```

```bash
tail -f *.log
tail -n 1000 *.log 
跟踪日志
```

```bash
find / -name .gitconfig
查找文件
```

```bash
cat *.log | grep "关键字"
根据错误名称筛选日志
```

```bash
curl、wget 模拟请求工具、下载工具。如
wget -r http://site 将下载整个站点
```

```bash
ab Apache服务器的性能测试工具
```

```bash
删除文件夹
rm -rf dir/*
```

```bash
查看启动项
systemctl list-unit-files
systemctl list-unit-files | grep enable
systemctl list-unit-files | grep docker
```

```bash
# 查看防火墙状态
firewall-cmd --state
systemctl status firewalld.service
# 开启防火墙
systemctl start firewalld.service
# 关闭防火墙
systemctl stop firewalld.service
# 允许防火墙开机启动
systemctl enable firewalld.service
# 禁止防火墙开机启动
systemctl disable firewalld.service
# 将指定端口配置到防护墙规则中
firewall-cmd --zone=public --add-port=端口号/tcp --permanent
# 重新加载防火墙
firewall-cmd --reload
# 关闭Selinux
vim /etc/sysconfig/selinux
SELINUX＝disabled
```

### 二、性能评估

###### 1.整机

```bash
查看CPU的加载
top
系统性能命令的精简版
uptime

load average三个值平均代表系统1分钟，五分钟，十五分钟的平均负载值，三个值相加除以三乘以100%高于60%，系统的负担压力重。
```

###### 2.CPU

```bash
查看CPU的加载
vmstat -n 2 3
每两秒采样一次，共计三次
显示参数us+sy超过80%,可能存在CPU不足

查看所有CPU核信息
mpstat -P ALL 2

每个进程使用的CPU的用量分解信息
pidstat -u 1 -p 进程编号
```

###### 3.内存

```bash
free
free -g
free -m
pidstat -p 进程号 -r 采样间隔秒数
```

###### 4.硬盘

```bash
使用df -h查看系统磁盘使用概况
lsblk 列出块设备信息
du 查看目录或者文件大小
```

###### 5.磁盘IO

```bash
iostat -xdk 2 3
看最后一个参数util，一秒钟有百分几的时间用于I/O操作。接近100%时，表示磁盘带宽跑满，需要优化程序或者增加磁盘

pidstat -d 采样间隔秒数 -p 进程号
```

###### 6.网络IO

```bash
ifstat 1
```

### 三、案例

###### 1.假如生产环境出现CPU占用过高，请谈谈你的分析思路和定位

```
1.结合Linux和JDK命令进行分析
	1.首先使用top命令找到CPU占比最高的
	2.然后ps -ef(ps -ef | grep java | grep -v grep)或者 jps -l
	3.定位到具体线程或者代码(ps -mp 进程编号 -o THREAD,tid,time)
	4.将需要的线程ID转换为16进制的格式(英文小写格式)
	5.jstack 进程ID | grep (16进制线程ID) -A60
```

