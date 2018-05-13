---
title: 第一章 MyBatis单表的CURD操作
date: 2018-05-08 15:25:25
tags: mybatis
category: mybatis

---

#### 1.1 简介

MyBatis是一个优秀的基于java的持久层框架，它内部封装了JDBC，使开发者只需关注SQL语句本身，不用再花费精力去处理诸如注册驱动、创建Connection、配置Statement等繁杂过程。

MyBatis通过xml或注解的方式将要执行的各种statement配置起来，并通过Java对象和Statement中的SQL动态参数进行映射生成最终执行的SQL语句，最后由MyBatis框架执行SQL并将结果映射成Java对象并返回。

#### 1.2 MyBatis与Hibernate

Hibernate框架是提供了全面的封装机制的“全自动”ORM，即实现了POJO和数据库表之间的映射，以及SQL的自动生成和执行。

相对于此，MyBatis只能算作是“半自动”ORM。其着力点，是在POJO与SQL语句之间的映射。也就是说，MyBatis并不会为程序员自动生成SQL语句。具体的SQL需要程序员自己编写，然后通过SQL语句映射文件，将SQL所需要的参数，以及返回的结果字段映射到指定的POJO。因此MyBatis成为了“全自动”ORM的一种有益补充。

与Hibernate相比，MyBaits具有以下几个特点：

（1）、在XML文件中配置SQL语句，实现了SQL语句与代码的分离，给程序的维护带来了很大便利。

（2）、因为需要程序员自己编写SQL语句，程序员可以结合数据库自身的特点灵活控制SQL语句，因此能够实现比Hibernate等全自动ORM框架更高的查询效率，能够完成复杂查询。

（3）、简单、易于学习，易于使用，上手快。

#### 1.3 Mapper动态代理

Mybatis框架抛开了Dao的实现类，直接定位到映射文件mapper中的相应SQL语句，对DB进行操作。这种对Dao的实现方式称为Mapper的动态代理方式。

Mapper动态代理方式无需程序员实现Dao接口。接口是由MyBatis结合映射文件自动生成的动态代理实现的。

动态代理的实现：

*	将mapper的namespace设为接口的全限制性类名

*	将mapper的sql语句的id设为接口的方法名


Dao的获取：

	SqlSession sqlSession = MyBatisUtils.getSqlSession();
	studentDao = sqlSession.getMappper(IStudentDao.class);
	
#### 1.4 CURD 单表操作

POJO: Student.java

	package com.bjpowernode.beans;
	
	public class Student {
		private Integer id;
		private String name;
		private int age;
		private double score;
		public Student() {
			super();
		}
		public Student(String name, int age, double score) {
			super();
			this.name = name;
			this.age = age;
			this.score = score;
		}
		public Integer getId() {
			return id;
		}
		public void setId(Integer id) {
			this.id = id;
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
		public double getScore() {
			return score;
		}
		public void setScore(double score) {
			this.score = score;
		}
		@Override
		public String toString() {
			return "Student [id=" + id + ", name=" + name + ", age=" + age + ", score=" + score + "]";
		}
		
	}

接口：IStudentDao.java

	package com.bjpowernode.dao;
	
	import java.util.List;
	import java.util.Map;
	
	import com.bjpowernode.beans.Student;
	
	public interface IStudentDao {
		//插入
		void insertStudent(Student student);
		//插入后获取id
		void insertStudentCatchId(Student student);
		//删除
		void deleteStudentById(int id);
		//修改
		void updateStudent(Student student);
		//查询所有学生对象
		List<Student> selectAllStudents();
		
		//根据id查询某个学生
		Student selectStudentById(int id);
		
		//根据名字模糊查询
		List<Student> selectStudentsByName(String name);
	}
	
工具类：

	package com.bjpowernode.utils;
	
	public class MyBatisUtils {
		private static SqlSessionFactory sqlSessionFactory;
		
		public static SqlSession getSqlSession() {
			try {
				InputStream inputStream = Resources.getResourceAsStream("mybatis.xml");
				if(sqlSessionFactory == null) {
					sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
				}
				return sqlSessionFactory.openSession();
			} catch (IOException e) {
				e.printStackTrace();
			}
			return null;
		}
	}	

数据库连接四要素配置文件：jdbc_mysql.properties
	
	jdbc.driver=com.mysql.jdbc.Driver
	jdbc.url=jdbc:mysql:///mydatabase
	jdbc.user=root
	jdbc.password=haojie_123456

主配置文件：mybatis.xml

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE configuration
	PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-config.dtd">
	
	<configuration>
		<!-- 注册DB连接四要素属性文件 -->
		<properties resource="jdbc_mysql.properties"></properties>
		
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

映射文件：mapper.xml

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="com.bjpowernode.dao.IStudentDao">
		
		<insert id="insertStudent" parameterType="Student">
			insert into student(name,age,score) values(#{name},#{age},#{score})
		</insert>
		
		<insert id="insertStudentCatchId">
			insert into student(name,age,score) values(#{name},#{age},#{score})
			<selectKey resultType="int" keyProperty="id" order="AFTER">
				select @@identity
			</selectKey>
		</insert>
		
		<delete id="deleteStudentById">
			delete from student where id=#{aaa} <!-- 这里的#{}仅仅是个占位符，里面放什么都行 -->
		</delete>
		
		<update id="updateStudent">
			update student set name=#{name}, age=#{age}, score=#{score} where id=#{id}
		</update>
		
		<select id="selectAllStudents" resultType="Student">
			select id,name,age,score from student
		</select>
		
		<!-- 
			resultType: 表示通过反映机制，调用set+属性名的方式，将结果封装为Student对象
			如果属性名和字段名不一致，表中的字段为tid,tname,tage,score，属性名为id,name,age,score
			解决方式一：字段别名 select tid id, tname name, tage age, score frome student
			解决方式二：resultMap 
				<resultMap type="Student" id="studentMapper">
					<id column="tid" property="id" />
					<result column="tname" property="name" />
					<result column="tage" property="age" />
				</resultMap>
				<select id="selectAllStudents" resultMap="studentMapper">
					select id,name,age,score from student
				</select>		
		 -->	
		
		<select id="selectStudentById" resultType="Student">
			select id,name,age,score from student where id=#{bbbbbb}
		</select>
	
		<select id="selectStudentsByName" resultType="Student">
			<!-- select id,name,age,score from student where name like concat('%',#{xxx},'%') -->
			select id,name,age,score from student where name like '%' #{xxx} '%'
		</select>
		
	</mapper>

测试文件：Mytest.java

	package com.bjpowernode.test;
		
	public class MyTest {
		
		private IStudentDao dao;
		private SqlSession sqlSession;
		
		@Before
		public void Before() {
			sqlSession = MyBatisUtils.getSqlSession();
			//dao:是一个动态代理
			dao = sqlSession.getMapper(IStudentDao.class);
		}
		
		@After
		public void after() {
			if(sqlSession != null) {
				sqlSession.close();
			}
		}
		
		@Test
		public void testInsertStudent() {
			Student student = new Student("zhangsan2", 24, 1);
			dao.insertStudent(student);
			sqlSession.commit();
		}
		
		@Test
		public void insertStudentCatchId() {
			Student student = new Student("lisi", 24, 94);
			System.out.println("插入前：student="+student);
			dao.insertStudentCatchId(student);
			System.out.println("插入后：student="+student);
		}
		
		@Test
		public void testDeleteStudentById() {
			dao.deleteStudentById(13);
		}
		
		@Test
		public void testUpdateStudent() {
			Student student = new Student("wangwu",25,95);
			student.setId(14);
			dao.updateStudent(student);
		}
		
		@Test
		public void testSelectAllStudents() {
			List<Student> students = dao.selectAllStudents();
			for(Student student:students) {
				System.out.println(student);
			}
		}
				
		@Test
		public void testSelectStudentById() {
			Student student = dao.selectStudentById(14);
			System.out.println(student);
		}
		
		@Test
		public void testSelectStudentByName() {
			List<Student> students = dao.selectStudentsByName("zhang");
			for(Student student:students) {
				System.out.println(student);
			}
		}
	}

#### 1.5 多条件查询问题 - 根据Map查询

接口：IStudentDao.java

	package com.bjpowernode.dao;
	
	public interface IStudentDao {	
		List<Student> selectStudentsByCondition(Map<String, Object> map);
	}

映射文件：

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="com.bjpowernode.dao.IStudentDao">
		
		
		<select id="selectStudentsByCondition" resultType="Student">
			<!-- #{}里面可以放对象的属性名，也可以放map的key，也可以直接通过对象获取属性：如student.score -->
			select id,name,age,score from student where name like '%' #{nameCondition} '%' and age > #{ageCondition}
		</select>
		
	</mapper>

测试类：MyTest.java

	package com.bjpowernode.test;
		
	public class MyTest {
		
		private IStudentDao dao;
		private SqlSession sqlSession;
		
		@Before
		public void Before() {
			sqlSession = MyBatisUtils.getSqlSession();
			//dao:是一个动态代理
			dao = sqlSession.getMapper(IStudentDao.class);
		}
		
		@After
		public void after() {
			if(sqlSession != null) {
				sqlSession.close();
			}
		}
		
		@Test
		public void testSelectStudentsByCondition() {
			 Map<String, Object> map = new HashMap<String, Object>();
			 map.put("nameCondition", "zhang");
			 map.put("ageCondition", 10);
			 
			 List<Student> students = dao.selectStudentsByCondition(map);
			 for(Student student:students) {
				 System.out.println(student);
			 }
		}
	}

#### 1.6 多条件查询问题 - 使用索引号

接口类

	package com.bjpowernode.dao;
		
	public interface IStudentDao {	
		List<Student> selectStudentsByCondition(String name, int age);
	}

映射文件：

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="com.bjpowernode.dao.IStudentDao">
		
		
		<select id="selectStudentsByCondition" resultType="Student">
			<!-- 
				#{}里面可以放:
				1、对象的属性名，
				2、参数为map时，可以放map的key，
				3、参数为map时，或key所对应的value为对象，则可以放入对象的属性如：Student.score 
				4、也可以放索引
				5、当#{}是个占位符时，可以放随意字符
			-->
			select id,name,age,score 
			from student 
			where name like '%' #{0} '%' 
			and age > #{1}
		</select>
		
	</mapper>

测试类

	package com.bjpowernode.test;
	
	public class MyTest {
		
		private IStudentDao dao;
		private SqlSession sqlSession;
		
		@Before
		public void Before() {
			sqlSession = MyBatisUtils.getSqlSession();
			//dao:是一个动态代理
			dao = sqlSession.getMapper(IStudentDao.class);
		}
		
		@After
		public void after() {
			if(sqlSession != null) {
				sqlSession.close();
			}
		}
		
		
		@Test
		public void testSelectStudentsByCondition() {
			
			 List<Student> students = dao.selectStudentsByCondition("zhang",23);
			 for(Student student:students) {
				 System.out.println(student);
			 }
		}
	}

#### 1.7 动态SQL

动态SQL，主要用于解决查询条件不确定的情况：在程序运行期间，根据用户提交的查询条件进行查询。提交的查询条件不同，执行的SQL语句不同。若将每种情况均逐一列出，对所有条件进行排列组合，将会出现大理的SQL语句。此时，可使用动态SQL来解决这样的问题。

##### 动态SQL - if、where、choose、foreach、foreach-list、foreach-泛型自定义类型list

测试类：

	package com.bjpowernode.test;
	
	public class MyTest {
		
		private IStudentDao dao;
		private SqlSession sqlSession;
		
		@Before
		public void Before() {
			sqlSession = MyBatisUtils.getSqlSession();
			//dao:是一个动态代理
			dao = sqlSession.getMapper(IStudentDao.class);
		}
		
		@After
		public void after() {
			if(sqlSession != null) {
				sqlSession.close();
			}
		}
		
		@Test
		public void testSelectStudentsByIf() {
			Student student = new Student("zhang",23,0);
			 List<Student> students = dao.selectStudentsByIf(student);
			 for(Student student1:students) {
				 System.out.println(student1);
			 }
		}
		
		@Test
		public void testSelectStudentsByWhere() {
			Student student = new Student("",0,0);
			 List<Student> students = dao.selectStudentsByWhere(student);
			 for(Student student1:students) {
				 System.out.println(student1);
			 }
		}
		
		@Test
		public void testSelectStudentsByChoose() {
			Student student = new Student("",0,0);
			 List<Student> students = dao.selectStudentsByChoose(student);
			 for(Student student1:students) {
				 System.out.println(student1);
			 }
		}
		
		@Test
		public void testForeach() {
			int[] ids = {14,16,18};
			List<Student> students = dao.selectStudentsByForeach(ids);
			for(Student student1:students) {
				System.out.println(student1);
			}
		}
		
		@Test
		public void testForeachList() {
			List<Integer> ids = new ArrayList<>();
			ids.add(14);
			ids.add(16);
			ids.add(18);
			List<Student> students = dao.selectStudentsByForeachList(ids);
			for(Student student1:students) {
				System.out.println(student1);
			}
		}
		
		@Test
		public void testForeachObject() {
			Student stu1 = new Student();
			stu1.setId(14);
	
			Student stu2 = new Student();
			stu2.setId(16);
			
			List<Student> stus = new ArrayList<>();
			stus.add(stu1);
			stus.add(stu2);
			
			List<Student> students = dao.selectStudentsByForeachObject(stus);
			for(Student student1:students) {
				System.out.println(student1);
			}
		}
	}

接口

	package com.bjpowernode.dao;
	
	public interface IStudentDao {
		List<Student> selectStudentsByIf(Student student);
		
		List<Student> selectStudentsByWhere(Student student);
	
		List<Student> selectStudentsByChoose(Student student);
		
		List<Student> selectStudentsByForeach(int[] ids);
	
		List<Student> selectStudentsByForeachList(List<Integer> ids);
	
		List<Student> selectStudentsByForeachObject(List<Student> students);
		
	}


映射文件

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="com.bjpowernode.dao.IStudentDao">
		
		<select id="selectStudentsByIf" resultType="Student">
			select id,name,age,score 
			from student 
			where 1 = 1 
			<if test="name != null and name !=''">
				and name like '%' #{name} '%'
			</if>
			<if test="age > 0">
				and age > #{age}
			</if>
		</select>
		
		<select id="selectStudentsByWhere" resultType="Student">
			select id,name,age,score 
			from student 
			<where>
				<if test="name != null and name !=''">
					and name like '%' #{name} '%'
				</if>
				<if test="age > 0">
					and age > #{age}
				</if>
			</where>
		</select>
		
		<!-- 
			若姓名不空，则按姓名查询；若年龄不空，则按年龄查询；都为空，不查询。
		 -->
		<select id="selectStudentsByChoose" resultType="Student">
			select id,name,age,score 
			from student 
			<where>
				<choose>
					<when test="name != null and name != ''">
						and name like '%' #{name} '%'
					</when>
					<when test="age > 0">
						and age > #{age}
					</when>
					<otherwise>
						1 > 2
					</otherwise>
				</choose>
			</where>
		</select>
		
		<select id="selectStudentsByForeach" resultType="Student">
			<!-- select id,name,age,score from student where id in (1,3,5) -->
			select id,name,age,score 
			from student 
			<if test="array.length > 0">
				where id in 
				<foreach collection="array" item="myid" open="(" close=")" separator=",">
					#{myid}
				</foreach>
			</if>
		</select>
		
		<select id="selectStudentsByForeachList" resultType="Student">
			<!-- select id,name,age,score from student where id in (1,3,5) -->
			select id,name,age,score 
			from student 
			<if test="list.size > 0">
				where id in 
				<foreach collection="list" item="myid" open="(" close=")" separator=",">
					#{myid}
				</foreach>
			</if>
		</select>
		
		<select id="selectStudentsByForeachObject" resultType="Student">
			select id,name,age,score 
			from student 
			<if test="list.size > 0">
				where id in 
				<foreach collection="list" item="stu" open="(" close=")" separator=",">
					#{stu.id}
				</foreach>
			</if>
		</select>
		
		<sql id="">
			
		</sql>
		
	</mapper>	
	
