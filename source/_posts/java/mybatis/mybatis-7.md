---
title: 第七章 MyBatis - 查询缓存
date: 2018-05-13 09:51:37
tags: mybatis
category: mybatis

---

#### 7.1 一级缓存与二级缓存

MyBatis的查询缓存机制，根据缓存区的作用域与生命周期，可划分为两种：一级缓存与二级缓存。

MyBatis查询缓存的作用域是根据映射文件mapper的namespace划分的，相同的namespace的mapper查询数据存放在同一个缓存区域。不同namespace下的数据互不干扰。无论是一级缓存还是二级缓存，都是按照namespace进行分别存放的。

但一、二级缓存的不同之处在于，SqlSession一旦关闭，则SqlSession中的数据将不存在。而二级缓存的生命周期会与整个应用同步，与SqlSession是否关闭无关。换句话说，一级缓存是在同一线程（同一SqlSession）间共享数据，而二级缓存是在不同线程（不同SqlSession）间共享数据。

#### 7.2 一级缓存

MyBatis一级缓存是基于org.apache.ibatis.cache.impl.PerpetualCache类的HashMap本地缓存，其作用域是SqlSession。在同一个SqlSession中两次执行相同的sql查询语句，第一次执行完毕后，会将查询结果写入到缓存中，第二次会从缓存中直接获取数据，而不再到数据库中进行查询，从而提高查询效率。

当一个SqlSession结束后，该SqlSession中的一级缓存也就不存在了。<strong>MyBatis默认一级缓存是开启状态，且不能关闭。</strong>

	public void testSelectStudentsByCondition() {

		Student student = dao.selectStudentById(20);
		System.out.println(student);

		Student students = dao.selectStudentById(20);
		System.out.println(students);
		 
	}
	
查看控制台
	
	[DEBUG][2018-05-13 10:08:15] com.bjpowernode.dao.IStudentDao.selectStudentById 159 ==>  Preparing: select id,name,age,score from students where id=? 
	[DEBUG][2018-05-13 10:08:15] com.bjpowernode.dao.IStudentDao.selectStudentById 159 ==> Parameters: 20(Integer)
	[DEBUG][2018-05-13 10:08:15] com.bjpowernode.dao.IStudentDao.selectStudentById 159 <==      Total: 1
	Student [id=20, name=zhangsan2, age=24, score=1.0]
	Student [id=20, name=zhangsan2, age=24, score=1.0]

sql语句只执行了一次，说明第二次是从缓存中读取的数据，证明了一级缓存是存在的。

#### 7.3 一级缓存 - 从缓存中查找数据的依据


 缓存底层对应的是map集合
 
 * key: 即查询依据，使用的ORM架构不同，查询依据不同。
 * value: 查询结果
 
一级缓存中读取数据的依据是：
 
 * mybatis的查询依据是：sql的id + sql语句
 * hibernate的查询依据是：查询结果的id
 
#### 7.4 一级缓存 - 增删改对一级缓存的影响
 
增、删、改都会清空一级缓存，无论是否提交。

#### 7.5 二级缓存 - 内置二级缓存的开启

由于MyBatis从缓存中读取数据的依据与SQL的id相关，所以MyBatis使用二级缓存的目的是为了防止同一查询（相同的Sql id、相同的Sql语句）的反复执行。

而Hibernate由于从缓存中读取数据的依据是查询结果的id，所以Hibernate使用二级缓存的目的是为了在多个查询间共享查询结果。比如第一次查询出了所有的学生，第二次查询id为5的学生，可以从缓存中直接读取。

MyBatis内置的二级缓存为org.apache.ibatis.cache.impl.PerpetualCache

<strong>内置二级缓存的开启：</strong>

(1)、映射文件中加cache

	<mapper namespace="com.bjpowernode.dao.IStudentDao">
		
		<cache />
		...
		...
	</mapper>

(2)、实体类实现序列化

Student.java

	public class Student implements Serializable {
		...
	}
	
测试类

	@Test
	public void testSelectStudentsByCondition() {
		
		sqlSession = MyBatisUtils.getSqlSession();
		dao = sqlSession.getMapper(IStudentDao.class);
		Student student = dao.selectStudentById(20);
		System.out.println(student);
		
		//清空一级缓存		
		sqlSession.close();
		
		sqlSession = MyBatisUtils.getSqlSession();
		dao = sqlSession.getMapper(IStudentDao.class);
		Student students = dao.selectStudentById(20);
		System.out.println(students);
		 
	}
	
查看控制台

	[DEBUG][2018-05-13 10:30:19] com.bjpowernode.dao.IStudentDao 62 Cache Hit Ratio [com.bjpowernode.dao.IStudentDao]: 0.0
	Sun May 13 10:30:19 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
	[DEBUG][2018-05-13 10:30:20] com.bjpowernode.dao.IStudentDao.selectStudentById 159 ==>  Preparing: select id,name,age,score from students where id=? 
	[DEBUG][2018-05-13 10:30:21] com.bjpowernode.dao.IStudentDao.selectStudentById 159 ==> Parameters: 20(Integer)
	[DEBUG][2018-05-13 10:30:21] com.bjpowernode.dao.IStudentDao.selectStudentById 159 <==      Total: 1
	Student [id=20, name=zhangsan2, age=24, score=1.0]
	[DEBUG][2018-05-13 10:30:21] com.bjpowernode.dao.IStudentDao 62 Cache Hit Ratio [com.bjpowernode.dao.IStudentDao]: 0.5
	Student [id=20, name=zhangsan2, age=24, score=1.0]

sql语句也只执行了一次，说明了第二次读取数据是从二级缓存中读取的。

缓存命中率

	Cache Hit Ratio [com.bjpowernode.dao.IStudentDao]: 0.5
	
#### 7.6 二级缓存 - 增删改对二级缓存的影响

1、增、删、改同样也会清空二级缓存

2、对于二级缓存的清空，实质上是对所查找key对应的value置为null，而并非将<key,value>对，即Entry对象删除。

3、从DB中进行select查询的条件是：

*	缓存中根本就不存在key
* 	缓存中存在该key所对应的Entry对象，但其value为null

#### 7.7 二级缓存 - 内置二级缓存的配置

	<cache eviction="FIFO" flushInterval="10800000" readOnly="true" size="512" />
	
<strong>eviction:</strong> 逐出策略。当二级缓存中的对象达到最大值时，就需要通过逐出策略将缓存中的对象移出缓存。默认为LRU。常用策略有：

*	FIFO：First In First Out，先进先出。

*	LRU：Least Recently Used：未被使用时间最长的。

	
<strong>flushInterval:</strong> 刷新缓存的时间间隔，单位毫秒。即清空缓存间隔时间，一般不指定，即当执行增、删、改时刷新缓存。

<strong>readOnly:</strong> 设置缓存中数据是否只读。只读的缓存会给所有调用者返回缓存对象的相同实例，因此这些对象是不能被修改的，这提供了很重要的性能优势。但读写的缓存会返回缓存对象的拷贝，速度会慢一些，但是安全，因此默认是false。

<strong>flushInterval:</strong> 二级缓存中可以存放的最多对象个数。默认是1024个。

#### 7.8 二级缓存 - 二级缓存的关闭

全局关闭

	<configuration>
		<!-- 注册DB连接四要素属性文件 -->
		<properties resource="jdbc_mysql.properties"></properties>
		
		<settings>
			<!-- 关闭二级缓存 -->
			<setting name="cacheEnabled" value="false"/>
		</settings>
		
		...
	
	</configuration> 

局部关闭

	<mapper namespace="com.bjpowernode.dao.IStudentDao">
		
		<cache />
		
		<select id="selectStudentById" useCache="false" resultType="Student">
			select id,name,age,score 
			from students 
			where id=#{xxx}
		</select>
		...
	</mapper>

#### 7.9 二级缓存 - 使用原则

#####（1）、多个namespace不要操作同一张表

由于二级缓存中的数据是基于namespace的，即不同的namespace中的数据互不干扰。若某个用户在某个namespace下对表执行了增删改操作，该操作只会引发当前namespace下的二级缓存的刷新，而对其它namespace下的二级缓存没有影响。这样的话，其它二级缓存中的数据依然是未更新的数据，也就出现了多个namespace中的数据不一致的现象。

##### （2）、不要在关联关系表上执行增删改操作

一个namespace一般是对同一个表进行操作，若表间存在关联关系，也就意味着同一个表可能就会出现在多个namespace中。

##### （3）、查询多于修改时使用二级缓存

在查询操作远多于增删改操作的情况下可以使用二级缓存。因为任何增删改操作都将刷新二级缓存，对二级缓存的频繁刷新将降低系统性能。

#### 7.10 二级缓存 - ehCache

mybatis的特长是sql操作，缓存数据管理不是其特长，为了提高缓存性能，mybatis允许使用第三方缓存产品。ecCache就是其中的一种。

注意，使用ehCache二级缓存，实体类无需实现序列化接口。

两个jar包：

一个是ehCache的核心jar包，一个是整合jar包。

下载地址：https://github.com/mybatis/ehcache-cache/releases

#### 7.11 二级缓存 - ehCache - 开启

（1）、导入jar包

（2）、映射文件

	<mapper namespace="com.bjpowernode.dao.IStudentDao">
		
		<cache type="org.mybatis.caches.ehcache.EhcacheCache" />
		...
	</mapper>	 

（3）、ehcache.xml

	<defaultCache
            maxElementsInMemory="10000"
            eternal="false"
            timeToIdleSeconds="120"
            timeToLiveSeconds="120"
            maxElementsOnDisk="10000000"
            diskExpiryThreadIntervalSeconds="120"
            memoryStoreEvictionPolicy="LRU">
        <persistence strategy="localTempSwap"/>
    </defaultCache>