---
title: 第二章 maven整合smm
date: 2018-06-03 11:48:47
tags: maven
category: maven

---

#### 一、创建maven project

1、创建maven project

配置安装目录：

preferences/maven/installations/add maven根目录

preferences/maven/user settings settings.xml目录

构建索引

windows/show view/others/maven/local repository

2、修正错误

补充web.xml文件

pom.xml中指定编译版本

#### 二、搭建环境

1、pom.xml添加依赖

2、搭建springmvc环境

web.xml

*	注册字符编码过滤器

*	注册中央调度器

3、搭建spring环境

web.xml

*	注册ServletContext监听器

监听器默认加载WEB-INF/applicationContext.xml

*	指定spring配置文件位置classpath

4、搭建mybatis环境

*	mybatis核心配置文件

5、spring与springmvc整合

spring配置文件中，注册处理器

6、spring与mybatis整合

*	注册数据源dataSource

*	注册SessionFactory

*	注册事务管理

*	生成Dao代理对象


