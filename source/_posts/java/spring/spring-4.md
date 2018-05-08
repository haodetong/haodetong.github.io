---
title: 第四章 Spring与IOC 基于注解的DI
keywords: spring
description: 基于注解的DI
date: 2018-05-05 11:00:10
category: spring
tags: spring

---

DI-annotation

对于注解DI，不再需要在spring配置文件中声明Bean实例。

##### （1）、导入AOP的jar包。

注解的后台实现用到了AOP编程。

####（2）、添加约束头

约束在spring-framework-reference\html\xsd-configuration.html文件中

#### (3)、组件扫描器

配置文件中添加

	<context:component-scan base-package="com.bjpowernode.di03"></context:component-scan>


Student.class
	
	@Component("myStudent")//组件，表示该类被容器所管理
	public class Student {
		@Value("zhangsan")
		private String name;
		@Value("23")
		private int age;
		
		private School school;
		
		...
	
	}

#### 4.1 组件扫描器的base-package

扫描com.bjpowernode这个包及其子包

	<context:component-scan base-package="com.bjpowernode"></context:component-scan>
	
扫描com.bjpowernode这个包的子包

	<context:component-scan base-package="com.bjpowernode.*"></context:component-scan>

#### 4.1 基于注解的DI - @Component相关注解

与@Component注解，功能相同但是意义不同的注解还有三个：

*	@Repository: 注解在Dao实现类上

*	@Service: 注解在Service实现类上

*	@Controller: 注解在SpringMVC的处理器上

#### 4.2 基于注解的DI - @Scope

不指定的话，默认也是singleton
	
	@Scope("prototype")
	@Component("myStudent")
	public class Student {
		@Value("zhangsan")
		private String name;
		@Value("23")
		private int age;

#### 4.3 基于注解的DI - 域属性的注入

byName方式的注入，要求@Autowired与@Qualifier联合使用

	@Scope("prototype")
	@Component("myStudent")
	public class Student {
		@Value("zhangsan")
		private String name;
		@Value("23")
		private int age;
		@Autowired
		@Qualifier("mySchool")
		private School school;//域属性的注解注入
		...
	}
		
#### 4.3 基于注解的DI - 域属性的注入 - 使用@Resource注解
	
@Resource //byType的方式注入
	
@Resource(name="mySchool") //byName方式的注解注入

	@Component("myStudent")	
	public class Student {
		...
		//@Resource //byType的方式注入
		@Resource(name="mySchool") //byName方式的注解注入
		private School school;//域属性的注解注入
		...
	}

#### 4.4 基于注解的DI - 使用Spring的JUnit4测试

##### （1）、导入jar包

spring与junit4的整合jar包：spring-test.4.1.RELEASE.jar

测试类
	
	@RunWith(SpringJUnit4ClassRunner.class)
	//指定配置文件
	@ContextConfiguration(locations="classpath:com/bjpowernode/di03/applicationContext.xml")
	public class MyTest {
		//注解注入student
		@Resource(name="myStudent")
		private Student student;
		@Test
		public void test01() {
			System.out.println(student.toString());
		}
	}

#### 4.5 Spring与IOC DI-XML的优先级要基于注解的DI

同时存在基于XML的DI和基于注解的DI，XML的优先级要高。

