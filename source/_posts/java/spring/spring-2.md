---
title: 第二章 spring与IOC
keywords: spring
description: spring与ioc
date: 2018-05-04 15:28:48
category: spring
tags: spring

---

控制反转（IOC,Inversion of control），是一个概念，是一种思想。指将传上由程序代码直接操控的对象调用权交给容器，通过容器来实现对象的装配和管理。

比较流行的两种实现方式有两种：

*	依赖查找：Dependency Lookup, DL，容器提供回调接口与上下文环境给组件，程序代码则需要提供具体的查找方式。比较典型的是依赖于JNDI服务接口（java naming and directory interrface）的查找。

*	依赖注入：Dependency Injection， DI，程序代码不做定位查找，这些工作由容器自行完成。


依赖注入是目前最优秀的解耦方式。依赖注入让spring的Bean之间以配置文件的方式组织在一起，而不是以硬编码的方式耦合到一起。

#### 2.1 创建spring容器

	public class MyTest {
		@Test
		public void test01() {
			SomeServiceImpl service = new SomeServiceImpl();
			service.doSome();
		}
		
		@Test
		public void test02() {
			//从当前应用的类路径下查找
			ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
			ISomeService service = (ISomeService) ac.getBean("myService");
			service.doSome();
		}
		
		@Test
		public void test03() {
			//从当前应用的根路径下查找
			ApplicationContext ac = new FileSystemXmlApplicationContext("src/applicationContext.xml");
			ISomeService service = (ISomeService) ac.getBean("myService");
			service.doSome();
		}
		
		@Test
		public void test04() {
			BeanFactory bf = new XmlBeanFactory(new ClassPathResource("applicationContext.xml"));
			ISomeService service = (ISomeService) bf.getBean("myService");
			service.doSome();
		}
		
	}

applicationContext与BeanFactory两种容器的区别：对于其中的Bean的创建时机不同。

*	applicationContext容器在进行初始化时，会将其中的所有Bean对象进行创建。
	
	缺点：占用系统资源（内存、cpu等）
	
	优点：响应速度快

*	BeanFactory容器中的对象，在容器初始化时并不会创建，而是在真正调用时才会被创建

	缺点： 响应速度慢
	
	优点： 不多占用系统资源

#### 2.2 Bean的装配

Bean的装配，即Bean的创建。

底层应用反射机制，通过调用无参构造器创建的Bean对象。

#### 2.3 动态工厂Bean

applicationContext.xml

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xsi:schemaLocation="
	        http://www.springframework.org/schema/beans 
	        http://www.springframework.org/schema/beans/spring-beans.xsd">
		
		<!-- 注册动态工厂 -->
	    <bean id="factory" class="com.bjpowernode.service.ServiceFactory" />
		
		<!-- 注册Service:动态工厂Bean -->
		<bean id="myService" factory-bean="factory" factory-method="getSomeService" />
		
	</beans>

动态工厂

	public class ServiceFactory {
		public ISomeService getSomeService() {
			return new SomeServiceImpl();
		}
	}

接口

	public interface ISomeService {
		void doSome();
	}

实现类

	public class SomeServiceImpl implements ISomeService {
	
		@Override
		public void doSome() {
			System.out.println("execute dosome() method");
		}
	
	}

测试类

public class MyTest {
	
	@Test
	public void test05() {
		ApplicationContext ac = new FileSystemXmlApplicationContext("src/applicationContext.xml");
		ISomeService service = (ISomeService) ac.getBean("myService");
		service.doSome();
	}
	
}

#### 2.4 静态工厂Bean

applicationContext.xml
	
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xsi:schemaLocation="
	        http://www.springframework.org/schema/beans 
	        http://www.springframework.org/schema/beans/spring-beans.xsd">
		
		<!-- 注册Service:静态工厂Bean -->
		<bean id="myService" class="com.bjpowernode.service.ServiceFactory" factory-method="getSomeService" />
		
	</beans>

静态工厂

	public class ServiceFactory {
		public static ISomeService getSomeService() {
			return new SomeServiceImpl();
		}
	}

#### 2.5 Bean的作用域

	
	<bean id="myService" class="com.bjpowernode.service.SomeServiceImpl" scope="singleton" />

`scope="singleton"` 单例模式：容器中对象的创建是在Spring容器初始化时就全部创建，是默认值

`scope="prototype"` 原型模式：容器中对象的创建时机不是在Spring容器初始化时创建，而是在代码中真正访问时才创建

	ISomeService service1 = (ISomeService) ac.getBean("myService");
	
	ISomeService service2 = (ISomeService) ac.getBean("myService");
	
默认，即单例模式下，service1与service2从容器中获取的是同一个对象

原型模式下，service1与service2分别创建新的对象

