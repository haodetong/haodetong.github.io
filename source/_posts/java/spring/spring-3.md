---
title: 第三章 Spring与IOC 基于XML的DI
keywords: spring
description: 基于XML的DI
date: 2018-05-05 08:53:03
category: spring
tags: spring

---

Bean实例在调用无参构造器创建了空值对象后，就要对Bean对象的属性进行初始化。初始化是由容器自动完成的，称为注入。常用方式分为设值注入和构造注入。

#### 3.1 基于xml-设值注入

通过set方法注入，基本数据类型用value，引用数据类型用ref。

applicationContext.xml

	<bean id="mySchool" class="com.bjpowernode.di01.School">
		<property name="name" value="清华大学" />
	</bean>
	
	<bean id="myStudent" class="com.bjpowernode.di01.Student">
		<property name="name" value="张三" />
		<property name="age" value="23"></property>
		<property name="school" ref="mySchool"></property>
	</bean>

School.class

	public class School {
		private String name;
		public void setName(String name) {
			this.name = name;
		}
		
	}

Student.class

	public class Student {
		
		private String name;
		private int age;
		private School school;
	
		public Student() {
			super();
		}
		
		public void setSchool(School school) {
			this.school = school;
		}
		
		public void setName(String name) {
			this.name = name;
		}
		
		public void setAge(int age) {
			this.age = age;
		}
	
		@Override
		public String toString() {
			return "Student [name=" + name + ", age=" + age + ", school=" + school + "]";
		}
	
	}

#### 3.2 基于xml-为集合属性赋值

Some.class

	public class Some {
		private School[] schools;
		private String[] myStrs;
		private List<String> myList;
		private Set<String> mySet;
		private Map<String,Object> myMap;
		private Properties myPros;
		...
		set 方法
		...
		@Override
		public String toString() {
			return "Some [schools=" + Arrays.toString(schools) + ", mystrs=" + Arrays.toString(myStrs) + ", myList="
					+ myList + ", mySet=" + mySet + ", myMap=" + myMap + ", myPros=" + myPros + "]";
		}
	}

applicationContext.xml

	<bean id="mySchool1" class="com.bjpowernode.di02.School">
		<property name="name" value="清华大学" />
	</bean>
	
	<bean id="mySchool2" class="com.bjpowernode.di02.School">
		<property name="name" value="北京大学" />
	</bean>
	
	<bean id="mySome" class="com.bjpowernode.di02.Some">
		<property name="schools">
			<array>
				<ref bean="mySchool1"/>
				<ref bean="mySchool2"/>
			</array>
		</property>
		
		<property name="myStrs">
			<array>
				<value>山东</value>
				<value>临沂</value>
			</array>
		</property>
		
		<property name="myList">
			<list>
				<value>平邑</value>
				<value>一村</value>
			</list>
		</property>
		
		<property name="mySet">
			<set>
				<value>一街道</value>
				<value>二街道</value>
			</set>
		</property>
		
		<property name="myMap">
			<map>
				<entry key="mobile" value="15011111111"></entry>
				<entry key="qq" value="11111"></entry>
			</map>
		</property>
		
		<property name="myPros">
			<props>
				<prop key="age">12</prop>
				<prop key="sex">femal</prop>
			</props>
		</property>
	</bean>

#### 3.3 基于xml-域属性-自动注入

##### 3.3.1 基于xml-`autowire="byName"`

会从容器中查找与Student类中域属性名scholl相同的Bean的id，并将该Bean对象自动注入给该域属性

		<bean id="school" class="com.bjpowernode.di01.School">
			<property name="name" value="清华大学" />
		</bean>
		
		<bean id="myStudent" class="com.bjpowernode.di01.Student" autowire="byName">
			<property name="name" value="张三" />
			<property name="age" value="23"></property>
		</bean>
	
##### 3.3.2 基于xml-`autowire="byType"`	

会从容器中查找与Student类中域属性类型School具有is-a关系的Bean，并将该Bean对象自动注入给该域属性

		<bean id="mySchool" class="com.bjpowernode.di01.School">
			<property name="name" value="清华大学" />
		</bean>
		
		<bean id="myStudent" class="com.bjpowernode.di01.Student" autowire="byType">
			<property name="name" value="张三" />
			<property name="age" value="23"></property>
		</bean>

##### 3.3.3 基于xml-DI-SPEL注入

	<bean id="myPerson" class="com.bjpowernode.di01.Person">
		<property name="pname" value="lisi"></property>
		<!-- 通过spring el表达式调用静态方法 -->
		<property name="page" value="#{T(java.lang.Math).random() * 50}"></property>
	</bean>
	
	<bean id="myStudent" class="com.bjpowernode.di01.Student">
		<!-- 调用其它Bean的属性 -->
		<property name="name" value="#{myPerson.pname}"></property>
		<!-- 运算 -->
		<property name="age" value="#{myPerson.page > 25 ? 25 : myPerson.page}"></property>
		<!-- 调用其它Bean的成员方法 -->
		<property name="age" value="#{myPerson.computeAge()}"></property>
	</bean>

#### 3.4 为应用指定多个Spring配置文件

##### (1)、平等关系的配置文件

两个配置文件：

*	com/bjpowernode/di01/spring-base.xml

*	com/bjpowernode/di01/spring-beans.xml

创建容器：

	String source01 = "com/bjpowernode/di01/spring-base.xml";
	String source02 = "com/bjpowernode/di01/spring-beans.xml";
	ApplicationContext ac = new ClassPathXmlApplicationContext(source01,source02);
	
通配符
	
	String source = "com/bjpowernode/di01/spring-*.xml";
	ApplicationContext ac = new ClassPathXmlApplicationContext(source);

##### (2)、包含关系的配置文件

三个配置文件：

*	com/bjpowernode/di01/applicationContext.xml

*	com/bjpowernode/di01/spring-base.xml

*	com/bjpowernode/di01/spring-beans.xml

applicationContext.xml

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	...
	>
	   <!--引入其它的配置文件-->     
		<import resource="classpath:com/bjpowernode/di01/spring-base.xml" />
		<import resource="classpath:com/bjpowernode/di01/spring-beans.xml" />
	
		<!--通配符-->
		<import resource="classpath:com/bjpowernode/di01/spring-*.xml" />

