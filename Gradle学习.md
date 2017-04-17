# Gradle构建

##一 什么是构建工具

###  一个可编程的工具，能够以可执行和有序的任务来表达满足需要的自动化过程。
#### 1.编译源代码
#### 2.运行测试
#### 3.拷贝Class文件到目标目录
#### 4.打包Class文件为Jar文件

### 这是一个完整的可自动化的过程，在没有构建工具之前，是由谁来做？——IDE。一个强大的IDE，以上的步骤都只需要按几个按钮，这让开发人员的生活变得很美好，完全集中在写出优秀的代码。

##二 Java世界的建构工具
### 在Java的世界里，目前在被使用的常用构建工具有三个：Ant，Maven，Gradle。

### Ant的核心是由Java编写，采用XML作为构建脚本，这样就允许你在任何环境下，运行构建。
### Ant基于任务链思想，任务之间定义依赖，形成先后顺序。缺点是使用XML定义构建脚本，导致脚本臃肿，Ant自身没有为项目构建提供指导，导致每个build脚本都不一样，开发人员对于每一个项目都需要去熟悉脚本内容，没有提供在Ant生态环境内的依赖管理工具。


### Maven团队意识到Ant的缺陷，采用标准的项目布局，和统一的生命周期，采用约定由于配置的思想，减少构建脚本需要的编写内容，活跃的社区，可以方便找到合适的插件，强大的依赖管理工具。缺点是采用默认的结构和生命周期，太过限制，编写插件扩展麻烦，XML作为构建脚本。

### 如果一个构架是你工具可以折中，同时拥有Ant和Maven的优点，是不是很爽？告诉你有，那就是Gradle。

## 三 Gradle 构建

###Gradle是一个基于Apache Ant和Apache Meven概念的项目自动化建构工具。它使用一种基于Groovy的特定领域语言来声明项目设置，而不是传统的XML。

#### 1.Gradle-Wrapper
### Gradle可以在没有安装Gradle的情况下使用，这时候就需要Gradle Wrapper了。Gradle Wrapper其实就是一个脚本文件，它会在没有安装Gradle的情况下为我们下载Gradle，之后我们就可以使用gradlew命令，像使用gradle一样来使用Gradle了。

### 创建项目完毕之后，会发现我们的项目中有如下一些文件：
####gradlew (Unix Shell 脚本)
####gradlew.bat (Windows批处理文件)
####gradle/wrapper/gradle-wrapper.jar (Wrapper JAR文件)
####gradle/wrapper/gradle-wrapper.properties (Wrapper属性文件)

### 我们就可以像使用gradle命令一样使用gradlew了。Gradle Wrapper会自动为我们下载合适的Gradle版本。默认情况下，下载位置是$USER_HOME/.gradle/wrapper/dists，如果设置了GRADLE_USER_HOME环境变量，那么就会下载到GRADLE_USER_HOME/wrapper/dists下。

#### #Mon Dec 28 10:00:20 PST 2015
#### distributionBase=GRADLE_USER_HOME
#### distributionPath=wrapper/dists
#### zipStoreBase=GRADLE_USER_HOME
#### zipStorePath=wrapper/dists
#### distributionUrl=https\://services.gradle.org/distributions/gradle-2.14.1-all.zip


### 上面是gradle-wrapper.properties文件中的内容，可以直接编辑文件修改Wrapper的版本，或者使用gradle wrapper --gradle-version 3.2.1来设置Wrapper的版本。

#### 2.build的生命周期
###Gradle的构建脚本生命周期具备三大步骤，如下：

####(1)_Initiallzation Gradle支持单项目和多项目构建，在初始化阶段Gradle决定哪些项目需要加入构建，并为这些需要加入构建的项目分别创建Project实例。实质为执行settings.gradle脚本。

####(2)_Configuration 配置阶段将整个build的Project及Task关系确定，它会建立一个有向图来描述Task之间的依赖关系。实质为解析每个被加入构建项目的build.gradle脚本。

####(3)_Execution 执行阶段就是Gradle的命令执行一些制定task名的task任务及其依赖task进行最终构建目标的生成等。


### 可以看见，生命周期其实和上面构建脚本Buid script的执行流程是可以关联上的。(上面关于Task方面的内容本文中不会使用。)


#### 3.设置脚本Settings script
### 在对工程进行配置(譬如多项目树构建)时Settings实例与settings.gradle文件一一对应，它用来进行一些项目设置的配置。这个文件一般放置在工程的根目录。
![](http://mmbiz.qpic.cn/mmbiz_png/y5HvXaQmpqkumTKnbalLYd3iauHKQFz9LPAkdj5QfzlI7HS77KicLsv8iaXLtVUN1rKFmF7cYjjwqZD3R46ia18ibgQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

### 多模块项目，就是在settings.build中添加模块名称。例如：
![](http://mmbiz.qpic.cn/mmbiz_png/y5HvXaQmpqkumTKnbalLYd3iauHKQFz9LtzfGSpxXeWY2icdrW8zJW7orYicJef8iaB4kqMJH3kBymSAMeSHic34T4A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

#### 4.顶层build.gradle文件
![](http://mmbiz.qpic.cn/mmbiz_png/y5HvXaQmpqkumTKnbalLYd3iauHKQFz9LfeK8DkH04Tyt5FgqzFm6518VtNYkvjvglK4P3yYbogbiaqrT9h08UYg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

### 构建的时候默认会配置上一个JCenter()仓库，也可以加上其他的仓库，比如，BRVAH这个第三方框架，就没有放在JCenter里面，因为这个仓库发布的流程比较复杂，时间也比较久，所以就发布在https://jitpack.io 仓库上面，所以在使用BRVAH的时候就要在allprojects 中添加jitpack仓库地址。

# 实战

## 1.BuildConfig和资源
### 通过配置灵活切换不同环境的接口地址

### 相信在eclipse开发的时候我们都是用的一下这种方法来切换接口地址的：
![](http://mmbiz.qpic.cn/mmbiz_png/y5HvXaQmpqkumTKnbalLYd3iauHKQFz9LeARvIq7r6IsEdAKrbVt8QRXgOOsic52rJyFIR7ASuNrRlwQbaEiaBHyA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

### 这种写法应该都不陌生，那在Gradle构建中，有没有可能用更好的方法来解决这个问题咧？答案是有可能的！
### 自SDK工具版本升级到17之后，构建工具都会生成一个叫作BuidConfig的类，该类包含一个按照构建类型设置值的DEBUG常量，可以定义其他的一些属性，Gradle提供了一个buildConfigField方法，下面就是顶一个String类型的BASE_URL常量，值为"http://debug.example.com/api"。

![](http://mmbiz.qpic.cn/mmbiz_png/y5HvXaQmpqkumTKnbalLYd3iauHKQFz9L3y6FaKxPfHXgXrct3hnZbB7eqEhADLBHoic73icxMAfNfEbsgrJRjRibw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

### 这里要注意的是添加String类型的数据的时候要添加转义的引号，不然是创建不成功的，例如：

![](http://mmbiz.qpic.cn/mmbiz_png/y5HvXaQmpqkumTKnbalLYd3iauHKQFz9Liblbv8nb30SWMwDo0YCBGiazsORzzPY4uKick5VM0BrZ0ibNRpKM5vUDWw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)


### 在这里还有一个问题，如果开发的是一个多模块项目，那么在library中定义这种属性，默认是不区分debug和release版本的。缺省情况下，无论你选用什么Build variant，库工程都只会打出release包，这是由于Gradle语言的限制造成的，但如果想强制使用debug，可以如下指定：
![](http://mmbiz.qpic.cn/mmbiz_jpg/y5HvXaQmpqk6Mcia1egibd7gsT1XXh27LJqibpOOKLGqghibZibgGmCvqjjwzlMr2OdXmetCv1h1ZFXz15MeOlnzBZA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1)

## 2.Gradle修改资源文件信息
###配置不同环境的资源文件
###同样的Gradle也提供了一个resValue()方法，下面就是定义了一个app_name的string资源。
![](http://mmbiz.qpic.cn/mmbiz_png/y5HvXaQmpqkumTKnbalLYd3iauHKQFz9Lt3I7fS3YmOG2ony9r7FFb46ctrBRrAvicuJlVf5N3EFa0X9stiahooCQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)


### 但是上面这种定义是错误的，build的时候会报以下这种错误：
![](http://mmbiz.qpic.cn/mmbiz_jpg/y5HvXaQmpqk6Mcia1egibd7gsT1XXh27LJuicsFXiaAXCOt4jE64SEO2vicI7YcXrt9K8yf23Dbp9xWicCsGhHYUs6BA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1)

###提示定义了重复的资源，所以再用resValue()的时候不要在.xml文件定义相同名字的资源数据。

## 3. gradle.properties 文件
### 解决频繁同步问题

### 相信大家在尝试上面的方法的时候有这样一个问题，那就是我们每修改一下 .gradle文件，AS工具就提示要"Sync Now"，这样是不是有点麻烦了咧？下面就可以用到gradle.properties文件来进一步的优化。

![](http://mmbiz.qpic.cn/mmbiz_png/y5HvXaQmpqkumTKnbalLYd3iauHKQFz9LDgMeDFAvzwaOVerEEsEoTpteVd5gljfmh6SZ1wQ8ZkIQsBk4icJ8XYA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

## 4.Gradle配置生成apk文件名

### 防止release覆盖问题，便于保存历史release记录
![](http://mmbiz.qpic.cn/mmbiz_jpg/y5HvXaQmpqk6Mcia1egibd7gsT1XXh27LJEIZzHXYyTLqTnicxoJlO0I8ibYXib2OKdBicickOUJICibyRj6oYWzjvZ5Pw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1)

