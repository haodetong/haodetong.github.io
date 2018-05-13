---
title: 第五章 MyBatis关联关系查询 - 多对多
date: 2018-05-12 19:06:57
tags: mybatis
category: mybatis

---

#### 5.1 多对多关联关系

一个学生可以选多门课程，而一门课程可以由多个学生选。这就是典型的多对多关系。所谓多对多，其实是由两个互反的一对多关系组成。

一般情况下，多对多关系都会通过一个中间表来建立。course表，middle表，student表。

定义实体类：Course.java

	package com.bjpowernode.beans;
	
	public class Course {
		private Integer cid;
		private String cname;
		private Set<Student> students;
		public Integer getCid() {
			return cid;
		}
		...
		getter and setter methods
		...
	}

定义实体类：Student.java

	package com.bjpowernode.beans;
	
	public class Student {
		private Integer sid;
		private String sname;
		private Set<Course> courses;
		public Integer getSid() {
			return sid;
		}
		...
		getter and setter methods
		...		
	}

接口

	package com.bjpowernode.dao;
	
	public interface IStudentDao {
		Student selectStudentById(int sid);
	}

映射文件

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="com.bjpowernode.dao.IStudentDao">
		
		<resultMap type="Student" id="studentlMapper">
			<id column="sid" property="sid"/>
			<result column="sname" property="sname"/>
			<collection property="courses" ofType="Course">
				<id column="cid" property="cid"/>
				<result column="cname" property="cname"/>
			</collection>
		</resultMap>
		
		<select id="selectStudentById" resultMap="studentlMapper">
			select sid, sname, cid, cname 
			from student, middle, course
			where sid=studentId and cid=courseId and sid=#{xxx}
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
		public void test01() {
			Student student = dao.selectStudentById(1);
			System.out.println(student);
		}
	}
