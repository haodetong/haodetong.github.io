---
title: 第一章 springmvc概述
date: 2018-05-22 15:57:36
tags: springmvc
category: springmvc

---

#### 1.1 springmvc程序

##### 1、配置web.xml

配置中央调度器

	<?xml version="1.0" encoding="UTF-8"?>
	<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" id="WebApp_ID" version="2.5">
		<display-name>01-springmvc-hello</display-name>
		
		<!-- 中央调度器 -->
		<servlet>
			<servlet-name>springMVC</servlet-name>
			<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
			<!-- 指定springmvc配置文件的位置及文件名 -->
			<init-param>
				<param-name>contextConfigLocation</param-name>
				<param-value>classpath:springmvc.xml</param-value>
			</init-param>
			<!-- 在Tomcat启动时直接创建当前Servlet -->
			<load-on-startup>1</load-on-startup>
		</servlet>
		
		<servlet-mapping>
			<servlet-name>springMVC</servlet-name>
			<url-pattern>*.do</url-pattern>
		</servlet-mapping>
		
		 <welcome-file-list>
		   <welcome-file>index.html</welcome-file>
		   <welcome-file>index.htm</welcome-file>
		   <welcome-file>index.jsp</welcome-file>
		   <welcome-file>default.html</welcome-file>
		   <welcome-file>default.htm</welcome-file>
		   <welcome-file>default.jsp</welcome-file>
		 </welcome-file-list>
	</web-app>

##### 2、配置springmvc.xml

配置视图解析器、注册处理器

	<?xml version="1.0" encoding="utf-8"?>
	
	<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context.xsd
	http://www.springframework.org/schema/aop
	http://www.springframework.org/schema/aop/spring-aop.xsd
	http://www.springframework.org/schema/tx
	http://www.springframework.org/schema/tx/spring-tx.xsd">
	
		<!-- 注册视图解析器 -->
		<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
			<property name="prefix" value="/WEB-INF/jsp/"></property>
			<property name="suffix" value=".jsp"></property>
		</bean>
		
		<!-- 注册处理器方式一：一个url对应一个controller 使用的处理器映射器：BeanNameUrlHandlerMapping -->
		<!-- <bean id="/my.do" class="com.bjpowernode.handlers.MyController"></bean> -->
		
		<!-- 注册处理器方式二：多个url对应一个controller -->	
			<!-- 注册处理器 -->
			<bean id="myController" class="com.bjpowernode.handlers.MyController"></bean>
			<!-- 注册处理器映射器: SimpleUrlHandlerMapping-->
			<bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
				<!-- 
				<property name="mappings">
					<props>
						<prop key="/hello.do">myController</prop>
						<prop key="/world.do">myController</prop>
					</props>
				</property>
				 -->
				 <property name="urlMap">
				 	<map>
				 		<entry key="/hello.do" value="myController"></entry>
				 		<entry key="/world.do" value="myController"></entry>
				 	</map>
				 </property>
			</bean>
			
	</beans>
	
##### 3、处理器

	package com.bjpowernode.handlers;
	
	import javax.servlet.http.HttpServletRequest;
	import javax.servlet.http.HttpServletResponse;
	
	import org.springframework.web.servlet.ModelAndView;
	import org.springframework.web.servlet.mvc.Controller;
	
	public class MyController implements Controller {
	
		@Override
		public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
			ModelAndView mv = new ModelAndView();
			//其底层执行的是request.setAttribute()方法
			mv.addObject("message", "hello world");
			mv.setViewName("welcome");
			return mv;
		}
	
	}

##### 4、视图

	<%@ page language="java" contentType="text/html; charset=UTF-8"
	    pageEncoding="UTF-8"%>
	<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
	<html>
	<head>
	<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
	<title>Insert title here</title>
	</head>
	<body>
	${message}
	aaaa
	</body>
	</html>

#### 1.2 `<url-pattern>`详解

中央调度器的url-pattern详解

（1）、建议写成*.do形式

（2）、不能写为/*

因为DispatcherServlet会将向动态页面的跳转请求，即向JSP页面的跳转请求也当作是一个普通的Controller请求。中央调度器会调用处理器映射器为其查找相应的处理器。当然是找不到的，在这种情况下所有的jsp页面跳转均会报404错误。

（3）、最好也不要写为/

因为DispatcherServlet会将向静态资源的请求，例如css,js,jpg,pgn等资源的获取请求，当作是一个普通的Controller请求。中央调度器会调用处理器映射器为其查找相应的处理器。当然也是找不到的，所以在这种情况下，所有的表态资源获取请求也均会报404错误。

（4）、不得不配成/时，解决静态资源无法访问的问题。

解决方式一：使用Tomcat默认的Servlet解决

	web.xml
	
	<servlet-mapping>
		<servlet-name>default</servlet-name>
		<url-pattern>*.jpg</url-pattern>
	</servlet-mapping>
	
	<servlet-mapping>
		<servlet-name>default</servlet-name>
		<url-pattern>*.csss</url-pattern>
	</servlet-mapping>

解决方式二：使用mvc的default-servlet-handler解决

 其底层调用的还是tomcat默认的servlet

	springmvc.xml
	
	<mvc:default-servlet-handler/>