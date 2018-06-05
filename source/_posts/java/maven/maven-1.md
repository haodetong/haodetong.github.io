---
title: 第一章 maven管理工具
date: 2018-06-01 16:52:37
tags: maven
category: maven

---

#### 1.1 maven的核心

依赖管理：对jar包统一管理

项目构建：对项目进行编译，测试，打包，部署

#### 1.2 maven安装

maven程序是java工发的，它的运行依赖jkd。

##### 1、maven下载： http://maven.apache.org/download.cgi

##### 2、配置环境变量

（1）、看看~/下是否有.bash_profile文件，如果没有就创建一个，环境变量要配置在这个文件中。

创建文件：

	touch ~/.bash_profile 

（2）、编辑bash_profile文件：

	vi ~/.bash_profile  

（3）、配置如下四个环境变量：
	
	# maven所在的目录  
	export M2_HOME=/Users/haojie/Downloads/Java/maven/apache-maven-3.3.9  
	# maven bin所在的目录  
	export M2=$M2_HOME/bin  
	# 将maven bin加到PATH变量中  
	export PATH=$M2:$PATH  
	# 配置JAVA_HOME所在的目录，注意这个目录下应该有bin文件夹，bin下应该有java等命令  
	JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_151.jdk/Contents/Home
	export JAVA_HOME  

（4）、在终端执行强制生效命令

	source ~/.bash_profile  
	
（5）、在新的命令行窗口执行mvn -v，如果正常显示了maven的版本等信息，就是配置成功了。


#### 1.3 配置本地仓库

/conf/settings.xml
	
	<localRepository>/Users/haojie/Downloads/Java/maven/maven_repository/</localRepository>

#### 1.4 maven的常用命令


*	clean:清理

将项目根目录下target目录清理掉

*	compile:编译

将于项目中.java文件编译为.class文件

*	test:测试

单元测试类名有要求：XxxxTest.java

将项目根目录下src/test/java目录下的单元测试类全部执行

*	package:打包

将项目打包到根目录下target目录

*	install:安装

打包jar包到本地仓库

#### 1.5 maven生命周期

在maven中存在三套生命周期，每一套生命周期相互独立，互不影响

1、CleanLifeCycle:清理生命周期

*	clean

2、defaultLifeCycle:默认生命周期

*	compile,test,package,install,deploy

3、siteLifeCycle：站点生命周期

*	site

在一套生命周期内，执行后面的命令，前面操作会自动执行。

#### 1.6 在eclipse中配置maven插件

1、Preferences/Maven/Installations/add

添加maven程序

2、Preferences/Maven/User Settings 

添加maven settins.xml文件

3、window/show view/other/maven

右键Local Repository(....)/rebuild index构建索引

#### 1.7 依赖范围

添加依赖范围：默认是compile

如果将servlet-api设置为compile，打包后包含servlet-api.jar，部署到tomcat后，跟tomcat中存在的servlet-api.jar包冲突，会导致运行失败。

因此，如果使用到tomcat自带jar包，需要将项目中依赖作用范围设置为:provided。

#### 1.8 传递依赖冲突解决

##### 1.8.1 maven自己调解原则

*	第一声明者优先原则

谁先定义的就用谁的传递依赖

*	路径近者优先原则

直接依赖级别高于传递依赖

##### 1.8.2 排除依赖

排除掉冲突中的某一个依赖

##### 1.8.3 版本锁定

	<dependencyManagement>
		<dependency>
			...		
		</dependency>
	</dependencyManagement>


