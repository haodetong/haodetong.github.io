---
title: 第二章 MyBatis关联关系查询 - 一对多
date: 2018-05-12 17:30:02
tags: mybatis
category: mybatis

---

#### 2.1 关联查询

当查询的内容涉及到具有关联关系的多个表时，就需要使用关联查询。根据表与表之间的关联关系的不同，关联查询分为四种：

（1）一对一关联查询

（2）一对多关联查询

（3）多对一关联查询

（4）多对多关联查询

日常工作中最常见的关联关系是一对多、多对一与多对多，一对一的解决方案与多对一的解决方案是相同的。

##### 外键定义在多方表中，反过来说，如果一个表里面有外键，那么和某一个其它表相比，它肯定充当着多方。

定义DB

*	country: cid, cname

*	minister: mid, mname, countryId 	
	


#### 2.2 一对多关联查询 - 通过多表连接查询方式实现

这里的一对多关联查询是指，在查询一方对象的时候，同时将其所关联的多方对象也都查询出来。

以国家Country与部长Minister间的一对多关系而言，查询某一个国家时，将这个国家所有的部长查询出来。

定义实体类：Country.java

	package com.bjpowernode.beans;
	
	import java.util.Set;
	
	public class Country {
		private Integer cid;
		private String cname;
		//关联属性
		private Set<Minister> ministers;
		
		public Country() {
			super();
		}
		
		...
		getter and setter methods
		...
		
	}

定义实体类：Minister.java	

	package com.bjpowernode.beans;
	
	public class Minister {
		private Integer mid;
		private String mname;
		
		public Minister() {
			super();
		}
	
		...
		getter and setter methods
		...
		
	}

接口：ICountryDao

	package com.bjpowernode.dao;
	
	public interface ICountryDao {
		Country selectCountryById(int cid);
	}

测试类：

	package com.bjpowernode.test;
	
	public class MyTest {
		
		private ICountryDao dao;
		private SqlSession sqlSession;
		
		@Before
		public void Before() {
			sqlSession = MyBatisUtils.getSqlSession();
			//dao:是一个动态代理
			dao = sqlSession.getMapper(ICountryDao.class);
		}
		
		@After
		public void after() {
			if(sqlSession != null) {
				sqlSession.close();
			}
		}
		
		@Test
		public void test01() {
			Country country = dao.selectCountryById(2);
			System.out.println(country);
		}
	}

映射文件：

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="com.bjpowernode.dao.ICountryDao">
		
		<resultMap type="Country" id="countryMapper">
			<id column="cid" property="cid"/>
			<result column="cname" property="cname"/>
			<collection property="ministers" ofType="Minister">
				<id column="mid" property="mid"/>
				<result column="mname" property="mname"/>
			</collection>
		</resultMap>
		
		<select id="selectCountryById" resultMap="countryMapper">
			select cid,cname,mid,mname
			from country,minister
			where countryId=cid and cid=#{xxx}
		</select>
		
	</mapper>

#### 2.3 一对多关联查询 - 通过多表单独查询方式实现

映射文件

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="com.bjpowernode.dao.ICountryDao">
		
		<!-- 这使用种方式比多表联合查询方式多，因为这种方式可以使用延迟加载 -->
		
		<select id="selectMinisterByCountry" resultType="Minister">
			select mid,mname from minister where countryId=#{000}
		</select>
		
		<resultMap type="Country" id="countryMapper">
			<id column="cid" property="cid"/>
			<result column="cname" property="cname"/>
			<collection property="ministers" 
						ofType="Minister" 
						select="selectMinisterByCountry"
						column="cid"
			/>
		</resultMap>
		
		<select id="selectCountryById" resultMap="countryMapper">
			select cid,cname from country where cid=#{xxx}
		</select>
		
	</mapper>




