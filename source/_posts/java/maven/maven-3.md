---
title: 第三章 maven拆分ssm
date: 2018-06-05 11:09:01
tags: maven
category: maven

---

#### 一、父工程

创建 maven project

打包方式 pom

pom.xml中定义ssm所依赖的所有jar包

将父工程发布到本地仓库

#### 二、子工程ssm-dao

1、创建 maven module 继承自父工程

2、打包方式 jar

3、将dao层相关的配置文件放到该工程下

*	jdbc.properties
* 	mybatis.xml
*  spring/applicationContext-basic.xml
*  spring/applicationContext-dao.xml

applicationContext-basic.xml注册数据源、sqlSessionFactory以及事务

applicationContext-dao.xml生成dao的代理对象

4、将dao层相关的代码放到该工程下

*	beans
*	dao

5、发布工程到本地仓库供子工程ssm-service依赖

#### 三、子工程ssm-service

1、创建 maven module 继承自父工程

2、打包方式 jar

3、将service层相关的配置文件放到该工程下

*	spring/applicationContext-service.xml

4、将service层相关的代码放到该工程下

* service接口及实现类

5、发布工程到本地仓库供子工程ssm-web依赖

#### 四、子工程ssm-web

1、创建 maven-module 继承自父工程

2、打包方式 war

3、将web层相关的配置文件放到该工程下

*	spring/applicationContext-web.xml(注册处理器)

4、将web层相关的代码放到该工程下

*	处理器

5、web.xml

	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath*:spring/applicationContext-*.xml</param-value>
	</context-param>

classpath*:指的是除了加载本项目下的spring/applicationContext-*.xml配置文件，也加载所依赖的jar包下的spring/applicationContext-*.xml配置文件。这样就能将dao层与service层的spring配置文件也加载进来。

#### 五、测试

1、测试dao层

	@RunWith(SpringJUnit4ClassRunner.class)
	@ContextConfiguration("classpath:spring/applicationContext-*.xml")
	public class IStudentDaoTest {
		
		@Autowired
		private IStudentDao studentDao;
		
		@Test
		public void test() {
			//fail("Not yet implemented");
			studentDao.insertStudent(new Student("zhangsan",22));
		}
	
	}

2、测试service层

	public class IStudentServiceTest {
	
		@Test
		public void testAddStudent() {
			
			ClassPathXmlApplicationContext classPathXmlApplicationContext = new ClassPathXmlApplicationContext("classpath*:spring/applicationContext-*.xml");
			IStudentService service = (IStudentService)classPathXmlApplicationContext.getBean("studentService");
			service.addStudent(new Student("lisi",23));
		}
	
	}

#### 六、maven运行方式 comcat:run

1、运行父工程 

父工程将各个子模块聚合到一起，将ssm-web打成war包发布到tomcat。

2、运行ssm-web工程

