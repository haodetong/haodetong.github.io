---
title: 第三章 MyBatis关联关系查询 - 多对一
date: 2018-05-12 18:35:04
tags: mybatis
category: mybatis

---

#### 3.1 多对一关联查询 - 通过多表连接查询方式实现


以部长Minister与国家Countr与间的多对一关系而言，查询部长时，将这个部长所属的国家查询出来。

DB数据模型不变，还是在多表minister中定义外键。

定义实体类：Country.java

	package com.bjpowernode.beans;
	
	public class Country {
		private Integer cid;
		private String cname;
		
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
		//关联属性
		private Country country;
		
		public Minister() {
			super();
		}
	
		public Minister(Integer mid, String mname) {
			super();
			this.mid = mid;
			this.mname = mname;
		}
	
		...
		getter and setter methods
		...	
	}

接口

	package com.bjpowernode.dao;
	
	public interface IMinisterDao {
		Minister selectMinisterById(int mid);
	}

映射文件：

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="com.bjpowernode.dao.IMinisterDao">
		
		<resultMap type="Minister" id="ministerMapper">
			<id column="mid" property="mid"/>
			<result column="mname" property="mname"/>
			<association property="country" javaType="Country">
				<id column="cid" property="cid"/>
				<result column="cname" property="cname"/>
			</association>
		</resultMap>
		
		<select id="selectMinisterById" resultMap="ministerMapper">
			select mid,mname,cid,cname
			from minister,country
			where countryId=cid and mid=#{ooo}
		</select>
		
	</mapper>

测试类：

	package com.bjpowernode.test;
		
	public class MyTest {
		
		private IMinisterDao dao;
		private SqlSession sqlSession;
		
		@Before
		public void Before() {
			sqlSession = MyBatisUtils.getSqlSession();
			//dao:是一个动态代理
			dao = sqlSession.getMapper(IMinisterDao.class);
		}
		
		@After
		public void after() {
			if(sqlSession != null) {
				sqlSession.close();
			}
		}
		
		@Test
		public void test01() {
			Minister minister = dao.selectMinisterById(2);
			System.out.println(minister);
		}
	}

#### 3.2 多对一关联查询 - 通过多表单独查询方式实现

映射文件：

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="com.bjpowernode.dao.IMinisterDao">
		
		<select id="selectCountryByCountryId" resultType="Country">
			select cid,cname from country where cid=#{ooo}
		</select>
		
		<resultMap type="Minister" id="ministerMapper">
			<id column="mid" property="mid"/>
			<result column="mname" property="mname"/>
			<association 
				property="country" 
				javaType="Country" 
				select="selectCountryByCountryId"
				column="countryId"
			/>
			
		</resultMap>
		
		<select id="selectMinisterById" resultMap="ministerMapper">
			select mid,mname,countryId from minister where mid=#{ooo}
		</select>
		
	</mapper>