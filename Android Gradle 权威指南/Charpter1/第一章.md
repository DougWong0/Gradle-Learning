# 1. 配置Gradle环境

## 1.1 Windows下环境配置


下载**Gradle-all**后解压, 解压目录如下.

![目录结构](https://i.imgur.com/oAFrNAQ.png)

**配置环境变量**
![环境变量](https://i.imgur.com/BbM4FtT.png)

**查看版本`gradle -v`**
![查看Gradle版本](https://i.imgur.com/qAqfHmv.png)

**确保配合好jdk**
![](https://i.imgur.com/eWKuiRs.png)

# 2. Hello World
**创建build.gradle文件添加如下内容**
```
task hello{
	doLast{
		println 'Hello World'
	}
}
```

然后执行如下命令: `gradle -q hello`

```
// 输出
Hello World
```

1. **build.gradle** 文件是Gradle默认的构建脚本文件, 执行**gradle** 的时候默认会加载当前目录下的**build.gradle**文件.

2. **注意 : 可以通过`-b`参数来设置加载的文件 :  `gradle -b FileName.gradle`**

3. 在Groovy中方法的调用可以省略括号. 使用一个空格代替. 同时双引号和单引号都是表示字符串.例如 : `println 'Hello World'`


# 3. Gradle Wrapper

## 3.1 生成Wrapper
Gradle 提供了内置的Wrapper task帮助我们生成Wrapper所需要的文件. 在一个目录中输入 `gradle wrapper` 即可生成
![](https://i.imgur.com/INSkTm3.png)

## 3.2 Wrapper 配置

**命令格式: `gradle wrapper  参数选项`**

参数名 | 说明
--- | ---
--gradle-version | 指定gradle的版本号.
--gradle-distribuition-url | 指定下载Gradle发行版的URL地址


**示例代码: `gradle wrapper --gradle-version 2.4`**

## 3.3 gradle-wrapper.properties
该文件中是Gradle Wrapper的相关配置

```
// 下载的Gradle压缩包解压后的主目录.
distributionBase=GRADLE_USER_HOME
// 相对于distributionBase解压后的Gradle压缩包路径.
distributionPath=wrapper/dists
// 同distributionBase , 不过用来存放zip包的.
zipStoreBase=GRADLE_USER_HOME
// 同distributionPath , 不过用来存放zip包的.
zipStorePath=wrapper/dists
// 压缩包下载地址. 
distributionUrl=https\://services.gradle.org/distributions/gradle-4.4-all.zip
```
**注意: 我们一般使用gradle-4.4-all.zip 不用 gradle-4.4-bin.zip, 使用前者可以查看源代码.**

## 3.4 自定义Wrapper Task
在build.gradle 中添加如下内容:
```
task wrapper(task: Wrapper){
	gradleVersion = '2.4'
}
```
通过定上面的task 在执行`gradle wrapper` 的时候就不需要添加`--gradle-version 2.4` 来指定版本了. 也可以定义定义其他属性.


# 4. Gradle日志

## 4.1 日志级别

**日志级别**

级别 | 描述
--- | ---
ERROR | 错误消息
QUIET | 重要消息
WARNING | 警告消息
LIFECYCLE | 进度消息
INFO | 信息消息
DEBUG | 调试消息

**用途**
```
# 输出QUEIT级别及其之上的日志级别.
gradle -q tasks
# 输出INFO级别及其之上的日志
gradle -i tasks
```

**日志开关选项**

开关选项 | 输出日志界别
--- | ---
无选项 | LIFECYCLE以上
-q或者-queit | QUEIT 以上
-i或者--info | INFO以上
-d或者--debug | DEBUG 以上 

##   4.2 输出错误堆栈信息
默认情况下堆栈信息是关闭的也就是不会打印.我们需要使用gradle命令打开.

**错误堆栈开关选项**

命令行选项 | 描述
--- | ---
无选项 | 关闭
-s或者--stacktrace | 输出关键性堆栈信息.
-S或者--full-stacktrace | 输出所有堆栈信息.

**一般使用-s 不适用-S, 后者输出信息太多.比较混乱.**

## 4.3 输出自己的调试信息
1. 使用`println '信息'`
2. 使用logger

```
logger.quiet('quiet 日志信息')
logger.error('错误消息')
logger.warn('警告消息')
logger.lifecycle('进度消息')
logger.info('info消息')
logger.debug('debug消息')
```

# 5. Gradle命令行

## 5.1 帮助命令
帮助命令
```
gradle -?
gradle -h
gradle -help
```

## 5.2 查看所有可执行的任务

使用`gradle tasks` 可以查看所有的可以执行的task
![](https://i.imgur.com/KNLG22L.png)

Gradle还内置了一个help task 可以让我们了解每一个task的使用帮助.
`gradle help --task tasks` 查看tasks的帮助的文档.
![](https://i.imgur.com/xUgtBPl.png)

从帮助文档中我们可以看到Task有什么用, 类型, 分组, 参数.等信息.


## 5.3 强制刷新依赖
```
// 强制刷新assemble的依赖, 更新缓存
gradle --refresh-dependencies assemble
```

## 5.4 多任务执行
```
# 执行多个任务, clean 和 jar任务.
gradle clean jar
```

## 5.5 使用命令缩写
比如有一个命令是connectCheck, 这个命令比较长, 我们可以使用驼峰式缩写, 只要能唯一标示命令即可. 比如使用cC.
```
task1: connectCheck
task2: closeCheck

使用cC就不行了, 会有歧义, 可以使用coC 或者clC
```




