---
title: 第六章 MyBatis - 延迟加载
date: 2018-05-12 19:19:53
tags: mybatis
category: mybatis

---

#### 6.1 延迟加载

MyBatis中的延迟加载，也称为懒加载，是指在进行<strong>关联查询</strong>时，按照设置的延迟规则推迟对关联对象的select查询。延迟加载可以有效的减少数据库压力。

需要注意的是，MyBatis的延迟加载只是对关联对象的查询有延迟设置，对于主加载对象都是直接执行查询语句的。


#### 6.2 关联对象加载时机

MyBatis根据对关联对象查询的select语句的执行时机，分为三种类型：直接加载、侵入式延迟加载与深度延迟加载。

<strong>直接加载：</strong> 执行完对主加载对象的select语句，马上执行对关联对象的select查询。

<strong>侵入式延迟：</strong> 执行完对主加载对象的查询时，不会执行对关联对象的select查询。但当要访问主加载对象的详情时，就会马上执行关联对象的select查询。即对关联对象的查询执行，侵入到了主加载对象的详情访问中。也可以这样理解：将关联对象的详情侵入到了主加载对象的详情中，即将关联对象的详情作为主加载对象的详情的一部分出现了。

<strong>深度延迟：</strong> 执行对主加载对象的查询时，不会执行对关联对象的查询。访问主加载对象的详情时也不会执行关联对象的select查询。只有当真正访问关联对象的详情时，才会执行对关联对象的select查询。

需要注意的是，延迟加载的应用要求，关联对象的查询与主加载对象的查询必须是分别进行的select语句，不能使用多表连接所进行的查询。因为，多表连接查询，其实质是对一张表的查询，对由多个表连接后形成的一张表的查询。会一次性将多张表的所有信息查询出来。

MyBatis中对于延迟加载设置，可以应用到一对一、一对多、多对一、多对多的所有关联关系查询中。

#### 6.3 懒加载配置

主配置文件：mybatis.xml

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE configuration
	PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-config.dtd">
	
	<configuration>
		<!-- 注册DB连接四要素属性文件 -->
		<properties resource="jdbc_mysql.properties"></properties>
		
		<!-- 设置整个应用所使用的常量 -->
		<settings>
			<!-- 懒加载总开关 -->
			<setting name="lazyLoadingEnabled" value="true"/>
			<!-- 侵入式延迟，默认值为true
				执行主加载对象的查询时dao.selectCountryById(2)，不会执行关联对象的查询。
				但是当访问主加载对象的详情时System.out.println(country.getCname())，就会马上执行关联对象的查询。
			 -->
			<setting name="aggressiveLazyLoading" value="false"/>
			<!-- 深度延迟 ：将侵入式延迟开关关闭
				执行对主加载对象的查询时dao.selectCountryById(2)，不会执行关联对象的查询。
				访问主加载对象的详情时System.out.println(country.getCname())，也不会执行关联对象的查询。
				只有当访问关联对象的详情时System.out.println(ministers.size())，才会执行关联查询。
			-->
		</settings>
		
		<!-- 设置别名 -->
		<typeAliases>
			<!-- 将指定包中所有类的简单类名当作别名 -->
			<package name="com.bjpowernode.beans"/>
		</typeAliases>
		
		<!-- 配置运行环境 -->
		<environments default="mysqlEM">
			<environment id="mysqlEM">
				<transactionManager type="JDBC"></transactionManager>
				<dataSource type="POOLED">
					<property name="driver" value="${jdbc.driver}"/>
					<property name="url" value="${jdbc.url}"/>
					<property name="username" value="${jdbc.user}"/>
					<property name="password" value="${jdbc.password}"/>
				</dataSource>
			</environment>
		</environments>
		
		<!-- 注册映射文件 -->
		<mappers>
			<mapper resource="com/bjpowernode/dao/mapper.xml" />	
		</mappers>
		
	</configuration>