### 1.下载

> 下载地址：https://www.jenkins.io/zh/download/

![image-20200524133542515](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524133542515.png)

### 2.安装

将它拷贝到我们的Linux目录/opt下

![image-20200524133650759](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524133650759.png)

```bash
# 启动jenkins
nohup java -jar /opt/jenkins.war --httpPort=8080 > /opt/jenkins.log 2>&1 &
# 切换到jenkins工作目录
cd /root/.jenkins/updates/
```

![image-20200524134516812](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524134516812.png)

```bash
# 安装插件提升下载速度,修改为清华源
vim default.json
# 使用vim的命令，如下，替换所有插件下载的url
:1,$s/http:\/\/updates.jenkins-ci.org\/download/https:\/\/mirrors.tuna.tsinghua.edu.cn\/jenkins/g
# 替换连接测试url
:1,$s/http:\/\/www.google.com/https:\/\/www.baidu.com/g
```

> 进入vim先输入`：`然后再粘贴上边的`：`后边的命令，注意不要写两个冒号！

修改完成保存退出`:wq`

访问Jenkins

> http://192.168.223.137:8080/

![image-20200524135507077](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524135507077.png)

```bash
# 查看初始化密码
cat /root/.jenkins/secrets/initialAdminPassword
```

![image-20200524135622788](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524135622788.png)

选择安装推荐的插件

![image-20200524135920150](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524135920150.png)

![image-20200524150553632](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524150553632.png)

待插件全部安装完成，然后点击使用admin账户继续

![image-20200524150858720](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524150858720.png)

实例配置

![image-20200524153249129](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524153249129.png)

开始使用Jenkins

![image-20200524153343625](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524153343625.png)

![image-20200524161209230](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524161209230.png)

### 3.配置

全局安全配置（Configure Global Security），授权策略生产环境下需另配，这里学习使用，设置为任何人可以做任何事

![image-20200524161722327](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524161722327.png)

全局工具配置（Global Tool Configuration），需先查看下java、maven、git路径

![image-20200524170859913](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524170859913.png)

![image-20200524162608212](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524162608212.png)

![image-20200524171125371](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524171125371.png)

### 4.新建项目

![image-20200524172120579](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524172120579.png)

代码仓库设置，添加完凭据，记得要选择

![image-20200524172529588](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524172529588.png)

![image-20200524172441607](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524172441607.png)

![image-20200524172651541](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524172651541.png)

构建环境

![image-20200524173830811](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524173830811.png)

![image-20200524174041658](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524174041658.png)

然后我们要增加构建后操作，首先需要在系统设置（Configure System）里，配置要部署的机器

![image-20200524174911834](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524174911834.png)

- Name： 标识的名字（随便你取什么）；
- Hostname： 需要连接ssh的主机名或ip地址，此处填写应用服务器IP（建议ip）；
- Username： 用户名；
- Remote Directory： 表示远程路径，登入到服务器后想打开哪个目录；
- Passphrase / Password： 密码（目标机器的密码）；

选择构建后操作

![image-20200524175133456](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524175133456.png)

![image-20200524180727943](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524180727943.png)

- name：选择之前添加的ssh设置；
- Source files：表示你要上传的文件的路径；
- Remove prefix： 这里指的是忽略前面的target/，远程服务器直接显示就是*.jar；
- Remote directory：远程目录，指定上传到远程服务器的哪个目录；
- Exec command：文本框表示你在文件上传完毕后想要执行的命令 不填也行 红色的警告可以无视；

开始构建

![image-20200524180918135](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524180918135.png)

查看日志显示成功

![image-20200524181905193](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524181905193.png)

### 5.查看结果

> 访问地址：http://192.168.223.135:8080/

![image-20200524181956077](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200524181956077.png)

部署成功！