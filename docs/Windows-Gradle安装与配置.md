### 1.下载

> 下载地址：https://gradle.org/releases/

![image-20200704144225043](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200704144225043.png)

### 2.解压缩

将文件解压到要安装的位置

![image-20200704144727897](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200704144727897.png)

### 3.配置Gradle仓库源

在Gradle安装目录下的 init.d 文件夹下，新建一个 init.gradle 文件，里面填写以下配置。

```
allprojects {
    repositories {
        maven { url 'file:///D:/developer_tools/Repository/RepGradle'}
        mavenLocal()
        maven { name "Alibaba" ; url "https://maven.aliyun.com/repository/public" }
        maven { name "Bstek" ; url "http://nexus.bsdn.org/content/groups/public/" }
        mavenCentral()
    }

    buildscript { 
        repositories { 
            maven { name "Alibaba" ; url 'https://maven.aliyun.com/repository/public' }
            maven { name "Bstek" ; url 'http://nexus.bsdn.org/content/groups/public/' }
            maven { name "M2" ; url 'https://plugins.gradle.org/m2/' }
        }
    }
}
```

repositories 中写的是获取 jar 包的顺序。先是本地的 Maven 仓库路径；接着的 mavenLocal() 是获取 Maven 本地仓库的路径，应该是和第一条一样，但是不冲突；第三条和第四条是从国内和国外的网络上仓库获取；最后的 mavenCentral() 是从Apache提供的中央仓库获取 jar 包。

### 4.配置环境变量

- 新建变量
  - 变量名：GRADLE_HOME
  - 变量值：Gradle解压的目录

![image-20200704145635120](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200704145635120.png)

- 新建变量
  - 变量名：GRADLE_USER_HOME
  - 变量值：自定义Gradle仓库目录或者Maven的仓库目录

![image-20200704145914951](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200704145914951.png)

- 添加变量

  - 变量名：Path
  - 变量值：%GRADLE_HOME%\bin

  ![image-20200704150140672](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200704150140672.png)

  ### 5.检查安装结果

  `gradle -v`

  ![image-20200704150428198](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200704150428198.png)

  ### 6.配置到IDEA

  在IDEA的Setting里打开"Build, Execution, Deployment"-"Build Tools"-"Gradle"配置仓库路径

  ![image-20200704154737372](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200704154737372.png)
  
  ![image-20200704151151198](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200704151151198.png)
  
  进入到gradle的项目中需要配置"Use Gradle from"

![image-20200704153434725](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20200704153434725.png)