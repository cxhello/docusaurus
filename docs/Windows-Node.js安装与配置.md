### 1.下载

> 下载地址：https://nodejs.org/zh-cn/download/

![image-20200412201424333](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200412201424333.png)

### 2.解压缩

将文件解压到要安装的位置，并新建两个目录

node-global：npm全局安装位置

node-cache：npm缓存路径

![image-20200412202420524](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200412202420524.png)

### 3.配置环境变量

将node.exe所在的目录添加到path环境变量，这样我们在使用命令行时就可以在任意路径使用node命令了，同时该目录下有一个npm.cmd文件，打开文件其实就是将我们的npm命令映射到node.exe npm-cli.js，由于存在该映射所以只要把node.exe 所在的目录添加到path环境变量，也可以在任何目录下执行npm install了。

![image-20200412202755048](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200412202755048.png)

这里Node.js就安装好了，可以在命令行输入如下命令进行测试：

```bash
node -v
npm -v
```

![image-20200412204819551](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200412204819551.png)

那么node-global ：npm全局安装位置，node-cache：npm 缓存路径又是怎么与npm发生关系呢？

通过如下命令进行配置：

```bash
npm config set prefix "D:\developer_tools\node-v12.16.2-win-x64\node-global"

npm config set cache "D:\developer_tools\node-v12.16.2-win-x64\node-cache"
```

执行npm命令进行测试：

`npm install webpack -g`

升级最新版本npm

`npm install -g npm `

会发现node-global下node_modules中多了webpack文件夹

![image-20200412204046649](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200412204046649.png)

webpack是用来打包的module，通常我们会在命令行中执行，而webpack同样在node-global中做了映射，所以只需要将node-global加入path环境变量即可。

![image-20200412204219525](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200412204219525.png)

现在可以在命令行中任意路径下执行node/npm/webpack命令了。还可以通过npm install yarn@latest -g 来安装yarn呢。