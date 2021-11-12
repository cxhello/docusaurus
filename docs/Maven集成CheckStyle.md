# Maven集成CheckStyle

最近在项目组里开发项目的时候，经常遇到项目里面代码格式不统一，merge 的时候经常冲突一大片，在网上搜索，查到了 Maven 可以集成 checkstyle 进行代码格式化审查。现将我的经验做以分享。

### 配置CheckStyle插件

在项目根目录新建一个 `config` 文件夹，将代码规约配置文件放到此路径下，当然你也可以根据自己的需求去自行定义

![image-20210121212720371](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210121212720371.png)

常见的语法配置如下：

> http://checkstyle.sourceforge.net/config.html

我这里用的是 `sun` 公司的代码规约，配置文件地址如下：

> https://github.com/checkstyle/checkstyle/tree/master/src/main/resources

在 Maven 中一个名为 `maven-checkstyle-plugin` 的插件，用于执行 [CheckStyle](http://checkstyle.sourceforge.net/) task。下面是一个简单的配置。在 Maven 工程中的 pom.xml 文件，添加内容如下：

```xml
<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-checkstyle-plugin</artifactId>
	<version>3.1.1</version>
	<configuration>
		<configLocation>config/checkstyle.xml</configLocation>
	</configuration>
	<executions>
		<execution>
			<id>checkstyle</id>
			<phase>validate</phase>
			<goals>
				<goal>check</goal>
			</goals>
			<configuration>
				<failOnViolation>true</failOnViolation>
			</configuration>
		</execution>
	</executions>
</plugin>
```

![image-20210121213350515](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210121213350515.png)

我们定义了在 Maven Lifecycle 的 validate 阶段执行 check task，并且如果发现有违反标准的情况就会 fail 当前的 build。

maven-checkstyle-plugin 内置了4种规范

- config/sun_checks.xml
- config/maven_checks.xml
- config/turbine_checks.xml
- config/avalon_checks.xml

其中 sun_checks.xml 为默认值。如果想要使用其他三种规范，则只需配置 configuration。

修改 pom 之后，就可运行 CheckStyle 检查，检查的报告文件在 target\checkstyle-result.xml 中

### 运行CheckStyle检查

```bash
mvn checkstyle:check
```

运行失败截图：

![image-20210121213819783](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210121213819783.png)

![image-20210121213843313](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210121213843313.png)

运行成功截图：

![image-20210121214349533](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210121214349533.png)

### 初始化 git hooks

**这一步主要是为了在 commit 代码之前，跑 Check Style，保证代码格式没问题，如果有问题的话就不能提交。**

在刚刚建好的 `config` 文件夹再建一个 `git-hooks` 文件夹，将 `pre-commit` 文件放到此路径下，pre-commit 文件内容如下：

```bash
#!/bin/sh
#set -x

echo "begin to execute hook"gst
mvn checkstyle:check

RESULT=$?

exit $RESULT
```

![image-20210121222030708](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210121222030708.png)

然后在项目的根目录下新建一个 `init.sh` 文件，init.sh 文件内容如下：

```bash
cp config/git-hooks/pre-commit .git/hooks/
chmod +x .git/hooks/pre-commit
```

![image-20210121222152434](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210121222152434.png)

> 以下演示的是 Mac/Linux 对应的操作，Window 用户需要手动将 `config/git-hooks` 目录下的`pre-commit` 文件拷贝到 项目下的 `.git/hooks/` 目录。

```
chmod +x ./init.sh
./init.sh
```

`init.sh` 这个脚本的主要作用是将 git commit 钩子拷贝到项目下的 `.git/hooks/` 目录，这样你每次 commit 的时候就会执行了。

如果代码格式有问题，提交的时候就会报错，如下图：

![image-20210121222237096](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20210121222237096.png)

> 代码地址：https://github.com/cxhello/springboot-admin

### 参考文章

> https://github.com/checkstyle/checkstyle

> https://github.com/Snailclimb/guide-rpc-framework

> https://blog.csdn.net/frank823/article/details/80787142
