---
title: 第四章 MyBatis关联关系查询 - 自关联
date: 2018-05-12 18:37:09
tags: mybatis
category: mybatis

---

#### 4.1 自关联查询

所谓自关联查询是指，自己既充当一方，又充当多方。例如，对于新闻栏目NewsLabel表，既有充当一方的父栏目，也有充当多方的子栏目。而反映到DB表中，只有一张表，这张表中具有一个外键pid，用于表示该栏目的父栏目。一级栏目没有父栏目，其外键值pid为0，而子栏目则具有外键值。

定义DB表 newslabel : 

*	id，name，pid
*	1， 新闻娱乐，0
*	2，	体育新闻，0
*	3，	NBA
* 	4，	CBA
*  5，	火箭，	3
*  6，	湖人，	3
*  7，	北京金隅，	4
*  8，	浙江广厦，	4
*  9，	青岛双星，	4
*  10，港台明星，	1
*  11，内地影展，	1

#### 4.2 自关联查询 - 以一对多的方式实现 - 查询指定栏目的所有子孙栏目

定义实体类：NewsLabel.java

	package com.bjpowernode.beans;
	
	import java.util.Set;
	
	//新闻栏目:当前的新闻栏目被看作是一方，即父栏目
	public class NewsLabel {
		private Integer id;
		private String name;
		//定义关联属性子栏目
		private Set<NewsLabel> children;
		...
		getter and setter methods
		...
	}

接口

	package com.bjpowernode.dao;
	
	public interface INewsLabelDao {
		List<NewsLabel> selectChildrenByParent(int pid);
	}

映射文件

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="com.bjpowernode.dao.INewsLabelDao">
		
		<!-- <select id="">
			select id,name from newslabel where pid=#{ooo}
		</select> -->
		
		<resultMap type="NewsLabel" id="newsLabelMapper">
			<id column="id" property="id"/>
			<result column="name" property="name"/>
			<collection property="children"
						ofType="NewsLabel"
						select="selectChildrenByParent"
						column="id"
			/>
		</resultMap>
		
		<select id="selectChildrenByParent" resultMap="newsLabelMapper">
			select id,name from newslabel where pid=#{xxx}
		</select>
		
	</mapper>

测试类

	package com.bjpowernode.test;
		
	public class MyTest {
		
		private INewsLabelDao dao;
		private SqlSession sqlSession;
		
		@Before
		public void Before() {
			sqlSession = MyBatisUtils.getSqlSession();
			//dao:是一个动态代理
			dao = sqlSession.getMapper(INewsLabelDao.class);
		}
		
		@After
		public void after() {
			if(sqlSession != null) {
				sqlSession.close();
			}
		}
		
		@Test
		public void test01() {
			List<NewsLabel> children = dao.selectChildrenByParent(2);
			for(NewsLabel newsLabel:children) {
				System.out.println(newsLabel);
			}
		}
	}

#### 4.3 自关联查询 - 以一对多的方式实现 - 查询指定栏目及其所有子孙栏目

实体类不变

接口

	import com.bjpowernode.beans.NewsLabel;
	
	public interface INewsLabelDao {
		NewsLabel selectNewsLabelById(int id);
	}

映射文件
	
	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="com.bjpowernode.dao.INewsLabelDao">
		
		<select id="selectNewsLabelByParent" resultMap="newsLabelMapper">
			select id,name from newslabel where pid = #{ooo}
		</select>
			
		<resultMap type="NewsLabel" id="newsLabelMapper">
			<id column="id" property="id"/>
			<result column="name" property="name"/>
			<collection property="children"
						ofType="NewsLabel"
						select="selectNewsLabelByParent"
						column="id"
			/>
		</resultMap>
		
		<select id="selectNewsLabelById" resultMap="newsLabelMapper">
			select id,name from newslabel where id=#{xxx}
		</select>
		
	</mapper>

#### 4.3 自关联查询 - 以多对一的方式实现 - 查询指定栏目及其父栏目

定义实体类

	package com.bjpowernode.beans;
	
	import java.util.Set;
	
	//新闻栏目:当前的新闻栏目被看作是多方，即子栏目
	public class NewsLabel {
		private Integer id;
		private String name;
		//定义关联属性父栏目
		private NewsLabel parent;
		...
		getter and setter methods
		...
	}

定义接口

	package com.bjpowernode.dao;
	
	public interface INewsLabelDao {
		NewsLabel selectNewsLabelById(int id);
	}

映射文件

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="com.bjpowernode.dao.INewsLabelDao">
		<!-- 
		<select id="" resultMap="">
			select id,name,pid from newslabel where id = #{ooo}
		</select>
		-->
		<resultMap type="NewsLabel" id="newsLabelMapper">
			<id column="id" property="id"/>
			<result column="name" property="name"/>
			<association property="parent" 
						 javaType="NewsLabel"
						 select="selectNewsLabelById"
						 column="pid"
			/>
		</resultMap>
		
		<select id="selectNewsLabelById" resultMap="newsLabelMapper">
			select id,name,pid from newslabel where id=#{xxx}
		</select>
		
	</mapper>

测试类

	package com.bjpowernode.test;

	public class MyTest {
		
		private INewsLabelDao dao;
		private SqlSession sqlSession;
		
		@Before
		public void Before() {
			sqlSession = MyBatisUtils.getSqlSession();
			//dao:是一个动态代理
			dao = sqlSession.getMapper(INewsLabelDao.class);
		}
		
		@After
		public void after() {
			if(sqlSession != null) {
				sqlSession.close();
			}
		}
		
		
		@Test
		public void test01() {
			NewsLabel newsLabel = dao.selectNewsLabelById(7);
			System.out.println(newsLabel);
		}
	}

