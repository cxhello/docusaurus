### yum源配置

```bash
# 1.安装wget
yum install -y wget
# 2.备份/etc/yum.repos.d/CentOS-Base.repo文件
cd /etc/yum.repos.d/
mv CentOS-Base.repo CentOS-Base.repo.back
# 3.查看系统版本,我的是CentOS Linux release 7.6.1810 (Core)
cat /etc/redhat-release
# 4.下载阿里云的Centos-7.repo文件
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
# 5.重新加载yum,非阿里云ECS用户会出现 Couldn't resolve host 'mirrors.cloud.aliyuncs.com' 信息，不影响使用。
yum clean all
yum makecache
```

