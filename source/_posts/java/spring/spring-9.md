---
title: 第九章 spring与JDBC模板
date: 2018-05-06 16:17:07
tags: spring 
category: spring

---

为了避免直接使用JDBC而带来的复杂且冗长的代码，Spring提供了一个强有力的模板类-JdbcTemplate来简化JDBC操作。并且，数据源DataSource对象与模板JdbcTemplate对象均通过Bean的形式定义在配置文件中，充分发挥了依赖注入的威力。

#### 9.1 导入jar包

spring-jdbc-*.jar

spring的事务 spring-tx-*.jar

c3p0: com.springsource.com.mchange.v2.c3p0-**.jar

dbcp: 
	
*	com.springsource.org.apache.commons.dbcp-*.osgi.jar

*	com.springsource.org.apache.commons.pool-*.jar

mysql驱动：mysql-connector-java-*-bin.jar


#### 9.3 定义实体类与DB表

student类

	package com.bjpowernode.beans;
	
	public class Student {
		private Integer id;
		private String name;
		private int age;
	
		public Student() {
			super();
		}
	
		
		public Student(Integer id, String name, int age) {
			super();
			this.id = id;
			this.name = name;
			this.age = age;
		}
	
	
		public Student(String name, int age) {
			super();
			this.name = name;
			this.age = age;
		}
	
	
		public void setId(Integer id) {
			this.id = id;
		}
	
	
		public Integer getId() {
			return id;
		}
	
	
		public String getName() {
			return name;
		}
	
		public void setName(String name) {
			this.name = name;
		}
	
		public int getAge() {
			return age;
		}
	
		public void setAge(int age) {
			this.age = age;
		}
	
	
		@Override
		public String toString() {
			return "Student [name=" + name + ", age=" + age + "]";
		}
		
	}


导出student.sql放入项目中

#### 9.4 定义Service

IStudentService接口

	package com.bjpowernode.service;
	
	import java.util.List;
	
	import com.bjpowernode.beans.Student;
	
	public interface IStudentService {
		
		void addStudent(Student student);
		void removeById(int id);
		void modifyStudent(Student student);
		
		List<String> findAllStudentsNames();
		String findStudentNameById(int id);
		
		List<Student> findAllStudents();
		Student findStudentById(int id);
		
	}

StudentServiceImpl实现类

	package com.bjpowernode.service;
	
	import java.util.List;
	
	import com.bjpowernode.Dao.IStudentDao;
	import com.bjpowernode.beans.Student;
	
	public class StudentServiceImpl implements IStudentService {
		
		private IStudentDao dao;
		
		public void setDao(IStudentDao dao) {
			this.dao = dao;
		}
	
		
		@Override
		public void addStudent(Student student) {
			dao.insertStudent(student);
		}
	
		@Override
		public void removeById(int id) {
			dao.deleteById(id);
		}
	
		@Override
		public void modifyStudent(Student student) {
			dao.updateStudent(student);
		}
	
		@Override
		public List<String> findAllStudentsNames() {
			return dao.selectAllStudentsNames();
		}
	
		@Override
		public String findStudentNameById(int id) {
			return dao.selectStudentNameById(id);
		}
	
		@Override
		public List<Student> findAllStudents() {
			return dao.selectAllStudents();
		}
	
		@Override
		public Student findStudentById(int id) {
			return dao.selectStudentById(id);
		}	
	}

	
#### 9.5 定义Dao

IStudentDao
	
	package com.bjpowernode.Dao;
	
	import java.util.List;
	
	import com.bjpowernode.beans.Student;
	
	public interface IStudentDao {
		
		
		void insertStudent(Student student);
		void deleteById(int id);
		void updateStudent(Student student);
		
		List<String> selectAllStudentsNames();
		String selectStudentNameById(int id);
		
		List<Student> selectAllStudents();
		Student selectStudentById(int id); 
	}

StudentDaoImpl

	package com.bjpowernode.Dao;
	
	import java.util.List;
	
	import org.springframework.jdbc.core.support.JdbcDaoSupport;
	
	import com.bjpowernode.beans.Student;
	
	//继承JdbcDaoSupport
	public class StudentDaoImpl extends JdbcDaoSupport implements IStudentDao {
		
		//增
		@Override
		public void insertStudent(Student student) {
			String sql = "insert into student(name,age) values(?,?)";
			this.getJdbcTemplate().update(sql, student.getName(), student.getAge());
		}
		
		//删
		@Override
		public void deleteById(int id) {
			String sql = "delete from student where id=?";
			this.getJdbcTemplate().update(sql,id);
		}
		
		//改
		@Override
		public void updateStudent(Student student) {
			String sql = "update student set name=?, age=? where id=?";
			this.getJdbcTemplate().update(sql, student.getName(), student.getAge(), student.getId());
		}
	
		//查
		@Override
		public List<String> selectAllStudentsNames() {
			String sql = "select name from student";
			return this.getJdbcTemplate().queryForList(sql, String.class);
		}
	
		@Override
		public String selectStudentNameById(int id) {
			String sql = "select name from student where id=?";
			return this.getJdbcTemplate().queryForObject(sql, String.class, id);
		}
	
		@Override
		public List<Student> selectAllStudents() {
			String sql = "select id,name,age from student";
			//手动封装查询的数据集
			return this.getJdbcTemplate().query(sql, new StudentRowMapper());
		}
	
		@Override
		public Student selectStudentById(int id) {
			String sql = "select id,name,age from student where id=?";
			return this.getJdbcTemplate().queryForObject(sql, new StudentRowMapper(), id);
		}
	
	
	}

#### 9.6 定义测试类

MyTest.java

	package com.bjpowernode.test;
		
	public class MyTest {
		
		private IStudentService service;
	
		@Before
		public void before() {
			String source = "applicationContext.xml";
			ApplicationContext ac = new ClassPathXmlApplicationContext(source);
			service = (IStudentService) ac.getBean("studentService");
		}
		
		//增
		@Test
		public void test01() {
			Student student = new Student("王五2", 26);
			service.addStudent(student);
		}
		
		//删
		@Test
		public void test02() {
			service.removeById(2);
		}
		
		//改
		@Test
		public void test03() {
			Student student = new Student(1,"李四2",23);
			service.modifyStudent(student);
		}
		
		//查所有学生的名字
		@Test
		public void test04() {
			List<String> names = service.findAllStudentsNames();
			System.out.println(names);
		}
		
		//查某个学生的名字
		@Test
		public void test05() {
			String name = service.findStudentNameById(1);
			System.out.println(name);
		}
		
		//查所有学生
		@Test
		public void test06() {
			List<Student> students = service.findAllStudents();
			for(Student student:students) {
				System.out.println(student);
			}
		}
		
		//查某个学生
		@Test
		public void test07() {
			Student student = service.findStudentById(1);
			System.out.println(student);
		}
		
	}


#### 9.7 配置

jdbc.properties 连接四要素

	jdbc.driver=com.mysql.jdbc.Driver
	jdbc.url=jdbc:mysql://127.0.0.1:3306/test
	jdbc.user=root
	jdbc.password=haojie_123456

applicationContext.xml

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xmlns:context="http://www.springframework.org/schema/context" xsi:schemaLocation="
	        http://www.springframework.org/schema/beans 
	        http://www.springframework.org/schema/beans/spring-beans.xsd
	        http://www.springframework.org/schema/context 
	        http://www.springframework.org/schema/context/spring-context.xsd">
		
		<!-- 注册数据源:Spring内置的连接池 -->
		<!-- <bean id="myDataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
			<property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
			<property name="url" value="jdbc:mysql://127.0.0.1:3306/mydatabase"></property>
			<property name="username" value="root"></property>
			<property name="password" value="haojie_123456"></property>
		</bean> -->
		
		<!-- 注册数据源:DBCP -->
		<!-- <bean id="myDataSource" class="org.apache.tomcat.dbcp.dbcp.BasicDataSource">
			<property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
			<property name="url" value="jdbc:mysql://127.0.0.1:3306/mydatabase"></property>
			<property name="username" value="root"></property>
			<property name="password" value="haojie_123456"></property>
		</bean> -->
		
		<!-- 注册数据源:C3P0 -->
		<!-- <bean id="myDataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
			<property name="driverClass" value="com.mysql.jdbc.Driver"></property>
			<property name="jdbcurl" value="jdbc:mysql://127.0.0.1:3306/mydatabase"></property>
			<property name="user" value="root"></property>
			<property name="password" value="haojie_123456"></property>
		</bean> -->
		
		<!-- 注册属性文件：从属性文件读取DB连接四要素：方式一 -->
		<!-- <bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
			<property name="location" value="classpath:jdbc.properties"></property>
		</bean> -->
		
		<!-- 注册属性文件：从属性文件读取DB连接四要素：方式二 -->
		<!-- <context:property-placeholder location="classpath:jdbc.properties"/> -->
		
		<bean id="myDataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
			<property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
			<property name="url" value="jdbc:mysql://127.0.0.1:3306/mydatabase"></property>
			<property name="username" value="root"></property>
			<property name="password" value="haojie_123456"></property>
		</bean>
		
		<!-- 将数据源注入给dao：方式一 -->
		
				<!-- 注册jdbctemplate -->
				<!-- <bean id="myJdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
					<property name="dataSource" ref="myDataSource"></property>
				</bean> -->
				
				<!-- 注册dao -->
				<!-- <bean id="studentDao" class="com.bjpowernode.Dao.StudentDaoImpl">
					<property name="jdbcTemplate" ref="myJdbcTemplate"></property>
				</bean> -->
		
		<!-- 将数据源注入给dao：方式二 -->
				
				<!-- 注册dao -->
				<bean id="studentDao" class="com.bjpowernode.Dao.StudentDaoImpl">
					<property name="dataSource" ref="myDataSource"></property>
				</bean>
			
		
		<!-- 注册实现类 -->
		<bean id="studentService" class="com.bjpowernode.service.StudentServiceImpl">
			<property name="dao" ref="studentDao"></property>
		</bean>
		
	</beans>

#### 9.8 Jdbc模板的对象是多例的

JdbcTemplate对象是多例的，即系统会为每一个使用模板对象的线程（方法）创建一个JdbcTemplate实例，并且在该线程（方法）结束时，自动释放JdbcTemplate实例。所以在每次使用JdbcTemplate对象时，都需要通过getJdbcTemplate()方法获取。

