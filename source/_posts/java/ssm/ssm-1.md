---
title: 第一章 搭建SSM开发环境
date: 2018-05-26 16:45:17
tags: ssm
category: ssm

---

#### 1.1 导入Jar包

（1）、mybatis的jar包

*	asm-4.2.jar
* 	cglib-3.1.jar
*  commons-logging-1.2.jar
*  log4j-1.2.17.jar
*  log4j-api-2.2.jar
*  log4j-core-2.2.jar
*  mybatis-3.3.0.jar
*  slf4j-api-1.7.12.jar
*  slf4j-log4j12-1.7.12.jar

（2）、ehcache的Jar包

*	ehcache-core-2.6.8.jar
* 	mybatis-ehcache-1.0.3.jar

（3）、Spring的Jar包

*	com.springsource.org.aopalliance-1.0.0.jar
* 	com.springsource.org.aspectj.weaver-1.6.8.RELEASE.jar
*  	spring-aop-4.2.1.RELEASE.jar
*	spring-aspects-4.2.1.RELEASE.jar
* 	spring-beans-4.2.1.RELEASE.jar
*	spring-context-4.2.1.RELEASE.jar
*  	spring-context-support-4.2.1.RELEASE.jar
*	spring-core-4.2.1.RELEASE.jar
* 	spring-expression-4.2.1.RELEASE.jar
*	spring-jdbc-4.2.1.RELEASE.jar
* 	spring-orm-4.2.1.RELEASE.jar
*	spring-tx-4.2.1.RELEASE.jar
*	spring-web-4.2.1.RELEASE.jar
* 	spring-webmvc-4.2.1.RELEASE.jar

（4）、mybatis与spring整合的Jar包

*	mybatis-spring-1.2.3.jar

（5）、JSON的Jar包

*	commons-beanutils-1.8.0.jar
	
*	commons-collections-3.2.1.jar
	
*	commons-lang-2.5.jar
	
*	commons-logging-1.1.1.jar
	
*	ezmorph-1.0.6.jar
	
*	json-lib-2.4-jdk15.jar

（6）、Jackson的Jar包

*	jackson-annotations-2.6.0-xh.jar

*	jackson-core-2.6.0-xh.jar

*	jackson-databind-2.6.0-xh.jar

*	jackson-jr-all-2.4.3-xh.jar

（7）、Hebernate验证器Jar包

*	hibernate-validator-5.1.0.Final.jar

*	jboss-logging-3.1.3.GA.jar

*	validation-api-1.1.0.Final.jar

（8）、其它Jar包

*	mysql-connector-java-5.1.44-bin.jar

*	c3p0-0.9.2.1

*	c3p0-oracle-thin-extras-0.9.2.1

*	mchange-commons-java-0.2.3.4

#### 1.2 配置式开发

##### 1.2.1 定义实体类及DB表

package com.bjpowernode.beans;

public class Student {
	private Integer id;
	private String name;
	private int age;
	
	public Student() {
		super();
		// TODO Auto-generated constructor stub
	}

	public Student(String name, int age) {
		super();
		this.name = name;
		this.age = age;
	}
	...
	getter and setter
	...
}

##### 1.2.2 定义表单页及处理器

index.jsp

	<form action="${pageContext.request.contextPath }/test/register.do" method="POST">
		姓名：<input type="text" name="name" /><br />
		年龄：<input type="text" name="age" /><br />
		<input type="submit" value="注册" />
	</form>

StudentController

	package com.bjpowernode.handlers;
	
	public class StudentController implements Controller {
		
		private IStudentService service;
		
		public void setService(IStudentService service) {
			this.service = service;
		}
	
		@Override
		public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
			
			String name = request.getParameter("name");
			String ageStr = request.getParameter("age");
			Integer age = Integer.valueOf(ageStr);
			Student student = new Student(name,age);
			
			service.addStudent(student);
			
			return new ModelAndView("/welcome.jsp");
		}
	
	}

##### 1.2.3 定义service

service interface

	package com.bjpowernode.service;
	public interface IStudentService {	
		void addStudent(Student student);
	}

service implements

	package com.bjpowernode.service;
	public class StudentServiceImpl implements IStudentService {
	
		private IStudentDao dao;
		
		public void setDao(IStudentDao dao) {
			this.dao = dao;
		}
	
		@Override
		public void addStudent(Student student) {
			dao.insertStudent(student);
		}
	
	}

##### 1.2.4 定义dao

dao interface

	package com.bjpowernode.dao;
	public interface IStudentDao {	
		void insertStudent(Student student);
	}

##### 1.2.5 定义mybatis两个配置文件

mybatis主配置文件：mybatis.xml

	<?xml version="1.0" encoding="UTF-8" ?> 
	<!DOCTYPE configuration
	PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-config.dtd">
	
	<configuration>
		<!-- 为实体类指定别名，简单类名 -->
		<typeAliases>
			<package name="com.bjpowernode.beans"/>
		</typeAliases>
		<!-- 注册映射文件 -->
		<mappers>
			<package name="com.bjpowernode.dao"/>
		</mappers>
	</configuration>

IStudentDao.xml

	<?xml version="1.0" encoding="UTF-8" ?> 
	<!DOCTYPE mapper
	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="com.bjpowernode.dao.IStudentDao">
		<insert id="insertStudent">
			insert into student(name,age) values(#{name},#{age})
		</insert>
	</mapper>

##### 1.2.6 注册数据源

jdbc.properties
	
	jdbc.driver=com.mysql.jdbc.Driver
	jdbc.url=jdbc:mysql:///mydatabase
	jdbc.user=root
	jdbc.password=haojie_123456

spring-db.xml

	<?xml version="1.0" encoding="utf-8"?>
	
	<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		http://www.springframework.org/schema/context/spring-context.xsd
		http://www.springframework.org/schema/aop
		http://www.springframework.org/schema/aop/spring-aop.xsd
		http://www.springframework.org/schema/tx
		http://www.springframework.org/schema/tx/spring-tx.xsd
		http://www.springframework.org/schema/mvc
		http://www.springframework.org/schema/mvc/spring-mvc.xsd">
		
		<!-- 注册c3p0数据源 -->
		<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
			<property name="driverClass" value="${jdbc.driver}"></property>
			<property name="jdbcUrl" value="${jdbc.url}"></property>
			<property name="user" value="${jdbc.user}"></property>
			<property name="password" value="${jdbc.password}"></property>
		</bean>
		 
		<!-- 注册属性文件 -->
		<context:property-placeholder location="classpath:resources/jdbc.properties" />
		
	</beans>

##### 1.2.7 生成Dao的代理对象

spring-mybatis.xml

	<?xml version="1.0" encoding="utf-8"?>
	
	<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		http://www.springframework.org/schema/context/spring-context.xsd
		http://www.springframework.org/schema/aop
		http://www.springframework.org/schema/aop/spring-aop.xsd
		http://www.springframework.org/schema/tx
		http://www.springframework.org/schema/tx/spring-tx.xsd
		http://www.springframework.org/schema/mvc
		http://www.springframework.org/schema/mvc/spring-mvc.xsd">
	
		<!-- 注册sqlSessionFactory -->
		<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
			<property name="configLocation" value="classpath:resources/mybatis.xml"></property>		
			<property name="dataSource" ref="dataSource"></property>
		</bean>
		
		<!-- 生成Dao的代理对象 -->
		<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
			<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"></property>
			<property name="basePackage" value="com.bjpowernode.dao"></property>
		</bean>
		
	</beans>

##### 1.2.8 注册service

spring-service.xml
	
	<?xml version="1.0" encoding="utf-8"?>
	
	<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context.xsd
	http://www.springframework.org/schema/aop
	http://www.springframework.org/schema/aop/spring-aop.xsd
	http://www.springframework.org/schema/tx
	http://www.springframework.org/schema/tx/spring-tx.xsd
	http://www.springframework.org/schema/mvc
	http://www.springframework.org/schema/mvc/spring-mvc.xsd">
	
		<bean id="studentService" class="com.bjpowernode.service.StudentServiceImpl">
			<property name="dao" ref="IStudentDao"></property>
		</bean>
	
	</beans>
	
##### 1.2.9 注册springmvc处理器

spring-mvc.xml

	<?xml version="1.0" encoding="utf-8"?>
	
	<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		http://www.springframework.org/schema/context/spring-context.xsd
		http://www.springframework.org/schema/aop
		http://www.springframework.org/schema/aop/spring-aop.xsd
		http://www.springframework.org/schema/tx
		http://www.springframework.org/schema/tx/spring-tx.xsd
		http://www.springframework.org/schema/mvc
		http://www.springframework.org/schema/mvc/spring-mvc.xsd">
		
		<!-- 注册处理器 -->
		<bean id="/test/register.do" class="com.bjpowernode.handlers.StudentController">
			<property name="service" ref="studentService"></property>
		</bean>
	
	</beans> 

##### 1.2.10 配置spring事务

spring-tx.xml

	<?xml version="1.0" encoding="utf-8"?>
	
	<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context.xsd
	http://www.springframework.org/schema/aop
	http://www.springframework.org/schema/aop/spring-aop.xsd
	http://www.springframework.org/schema/tx
	http://www.springframework.org/schema/tx/spring-tx.xsd
	http://www.springframework.org/schema/mvc
	http://www.springframework.org/schema/mvc/spring-mvc.xsd">
		
		<!-- 注册事务管理器 -->
		<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
			<property name="dataSource" ref="dataSource"></property>
		</bean>
		
		<!-- 注册事务通知 -->
		<tx:advice id="txAdvice">
			<tx:attributes>
				<tx:method name="add*" isolation="DEFAULT" propagation="REQUIRED"/>
			</tx:attributes>
		</tx:advice>
		
		<!-- AOP配置 -->
		<aop:config>
			<aop:pointcut expression="execution(* *..service.*.*(..))" id="studentPointcut"/>
			<aop:advisor advice-ref="txAdvice" pointcut-ref="studentPointcut"/>
		</aop:config>
		
	</beans>

#### 1.3 注解式开发

#### 1.3.1 将springmvc改为注解

springmvc.xml

	<?xml version="1.0" encoding="utf-8"?>
	
	<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		http://www.springframework.org/schema/context/spring-context.xsd
		http://www.springframework.org/schema/aop
		http://www.springframework.org/schema/aop/spring-aop.xsd
		http://www.springframework.org/schema/tx
		http://www.springframework.org/schema/tx/spring-tx.xsd
		http://www.springframework.org/schema/mvc
		http://www.springframework.org/schema/mvc/spring-mvc.xsd">
		
		<!-- 注册处理器 -->
		<!-- 
		<bean id="/test/register.do" class="com.bjpowernode.handlers.StudentController">
			<property name="service" ref="studentService"></property>
		</bean>
	 	-->
	 	
	 	<!-- 注册组件扫描器 -->
	 	<context:component-scan base-package="com.bjpowernode.handlers"></context:component-scan>
	 	
	</beans>

StudentController

*	请求路径

*	处理器

*	依赖注入

============

	package com.bjpowernode.handlers;
	
	@Controller
	@RequestMapping("/test")
	public class StudentController {
		
		@Autowired
		@Qualifier("studentService")
		//@Resource(name="studentService")
		private IStudentService service;
		
		public void setService(IStudentService service) {
			this.service = service;
		}
	
		@RequestMapping("/register.do")
		public ModelAndView doRegister(String name, int age) {
			
			Student student = new Student(name,age);
			
			service.addStudent(student);
			
			return new ModelAndView("/welcome.jsp");
		}
	}

#### 1.3.2 将spring改为注解

spring-service.xml

	<?xml version="1.0" encoding="utf-8"?>
	
	<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context.xsd
	http://www.springframework.org/schema/aop
	http://www.springframework.org/schema/aop/spring-aop.xsd
	http://www.springframework.org/schema/tx
	http://www.springframework.org/schema/tx/spring-tx.xsd
	http://www.springframework.org/schema/mvc
	http://www.springframework.org/schema/mvc/spring-mvc.xsd">
		<!-- 
		<bean id="studentService" class="com.bjpowernode.service.StudentServiceImpl">
			<property name="dao" ref="IStudentDao"></property>
		</bean>
		-->
		
		<!-- 注册组件扫描器 -->
		<context:component-scan base-package="com.bjpowernode.service"></context:component-scan> 
		 
	</beans>
	
student service implements

	package com.bjpowernode.service;
	
	@Service("studentService")
	public class StudentServiceImpl implements IStudentService {
		
		@Resource(name="IStudentDao")
		private IStudentDao dao;
		
		public void setDao(IStudentDao dao) {
			this.dao = dao;
		}
	
		@Override
		@Transactional
		public void addStudent(Student student) {
			dao.insertStudent(student);
		}
	
	}

spring-tx.xml

	<?xml version="1.0" encoding="utf-8"?>
	
	<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context.xsd
	http://www.springframework.org/schema/aop
	http://www.springframework.org/schema/aop/spring-aop.xsd
	http://www.springframework.org/schema/tx
	http://www.springframework.org/schema/tx/spring-tx.xsd
	http://www.springframework.org/schema/mvc
	http://www.springframework.org/schema/mvc/spring-mvc.xsd">
		
		<!-- 注册事务管理器 -->
		<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
			<property name="dataSource" ref="dataSource"></property>
		</bean>
		
		<!--  
		注册事务通知
		<tx:advice id="txAdvice">
			<tx:attributes>
				<tx:method name="add*" isolation="DEFAULT" propagation="REQUIRED"/>
			</tx:attributes>
		</tx:advice>
		
		AOP配置
		<aop:config>
			<aop:pointcut expression="execution(* *..service.*.*(..))" id="studentPointcut"/>
			<aop:advisor advice-ref="txAdvice" pointcut-ref="studentPointcut"/>
		</aop:config>
		-->
		
		<!-- 注册事务驱动 -->
		<tx:annotation-driven transaction-manager="transactionManager" />
		
	</beans>