---
title: 第十章 Spring事务管理
date: 2018-05-08 09:50:30
tags: spring
category: spring

---

#### 10.1 事务管理器接口

事务管理器是PlatformTransactionManager接口对象。其主要用于完成事务的提交、回滚及获取事务的状态信息。

##### 常用的两个实现类

*	DataSourceTransactionManager：使用JDBC或iBatis进行持久化数据时使用。

*	HibernateTransactionManager：使用Hibernate进行持久化数据时使用。

##### Spring的回滚方式

Spring事务的默认回滚方式是：发生运行异常时回滚，发生受查异常时提交。

运行异常指的是继承自RuntimeException的异常类，受查异常指的是继承自Exception的异常类。

#### 10.2 事务定义接口

TransactionDefinition

##### 隔离级别：

* 	static int	ISOLATION_DEFAULT          0

*	static int	ISOLATION_READ_COMMITTED   2

*	static int	ISOLATION_READ_UNCOMMITED  1 

*	static int	ISOLATION_REPEATABLE_READ  4

*	static int	ISOLATION_SERIALIZABLE     8

MYSQL默认的是可重复读，ORACLE默认的是读已提交

##### 事务的传播行为

定义了七个事务传播行为。

doSome()方法调用doOther()方法，或doSome()方法存在于事务中，则doOther()方法加入到该事务中执行；若doSome()方法在调用doOther()方法时没有在事务内执行，则doOther()方法会创建一个事务，并在其中执行。

#### 10.3 程序举例

两个实体：银行账户Account与股票账户Stock。当要购买股票时，从Account中扣除相应金额的存款，同时在Stock中增加相应的股票数量。在这个过程中，抛出一个用户自定义的异常，使两个操作回滚。

#### 10.4 定义实体及DB表

Account.java

	package com.bjpowernode.beans;
	
	public class Account {
		private Integer aid;
		private String aname;
		private double balance;
		public Account() {
			super();
			// TODO Auto-generated constructor stub
		}
		public Account(String aname, double balance) {
			super();
			this.aname = aname;
			this.balance = balance;
		}
		public Integer getAid() {
			return aid;
		}
		public void setAid(Integer aid) {
			this.aid = aid;
		}
		public String getAname() {
			return aname;
		}
		public void setAname(String aname) {
			this.aname = aname;
		}
		public double getBalance() {
			return balance;
		}
		public void setBalance(double balance) {
			this.balance = balance;
		}
		@Override
		public String toString() {
			return "Account [aid=" + aid + ", aname=" + aname + ", balance=" + balance + "]";
		}
		
	}

Stock.java

	package com.bjpowernode.beans;
	
	public class Stock {
		private Integer sid;
		private String sname;
		private int count;
		
		public Stock() {
			super();
			// TODO Auto-generated constructor stub
		}
	
		public Stock(String sname, int count) {
			super();
			this.sname = sname;
			this.count = count;
		}
	
		public Integer getSid() {
			return sid;
		}
	
		public void setSid(Integer sid) {
			this.sid = sid;
		}
	
		public String getSname() {
			return sname;
		}
	
		public void setSname(String sname) {
			this.sname = sname;
		}
	
		public int getCount() {
			return count;
		}
	
		public void setCount(int count) {
			this.count = count;
		}
	
		@Override
		public String toString() {
			return "Stock [sid=" + sid + ", sname=" + sname + ", count=" + count + "]";
		}
		
	}

#### 10.5 定义service

业务接口

	package com.bjpowernode.service;
	
	public interface IBuyStockService {
		void openAccount(String aname, double money);
		void openStock(String sname, int amount);
		void buyStock(String aname, double money, String sname, int amount) throws BuyStockException;
	}


业务实现类

	package com.bjpowernode.service;
	
	import com.bjpowernode.Dao.IAccountDao;
	import com.bjpowernode.Dao.IStockDao;
	
	public class BuyStockServiceImpl implements IBuyStockService {
		private IAccountDao adao;
		private IStockDao sdao;
		
		public void setAdao(IAccountDao adao) {
			this.adao = adao;
		}
	
		public void setSdao(IStockDao sdao) {
			this.sdao = sdao;
		}
	
		@Override
		public void openAccount(String aname, double money) {
			adao.insertAccount(aname, money);
		}
	
		@Override
		public void openStock(String sname, int amount) {
			sdao.insertStock(sname, amount);
		}
	
		@Override
		public void buyStock(String aname, double money, String sname, int amount) throws BuyStockException {
			boolean isBuy = true;
			adao.updateAccount(aname, money, isBuy);
			if(true) {
				throw new BuyStockException("购买股票异常");
			}
			sdao.updateStock(sname, amount, isBuy);
		}
	
	}

#### 10.6 定义Dao

IAccountDao

	package com.bjpowernode.Dao;
	
	public interface IAccountDao {
	
		void insertAccount(String aname, double money);
	
		void updateAccount(String aname, double money, boolean isBuy);
	
	}


AccountDaoImpl

	package com.bjpowernode.Dao;
	
	import org.springframework.jdbc.core.support.JdbcDaoSupport;
	
	public class AccountDaoImpl extends JdbcDaoSupport implements IAccountDao {
	
		@Override
		public void insertAccount(String aname, double money) {
			String sql = "insert into account(aname, balance) values(?,?)";
			this.getJdbcTemplate().update(sql, aname, money);
		}
	
		@Override
		public void updateAccount(String aname, double money, boolean isBuy) {
			String sql = "update account set balance = balance + ? where aname=?";
			if(isBuy) {
				sql = "update account set balance = balance - ? where aname=?";
			}
			this.getJdbcTemplate().update(sql, money, aname);
		}
	
	}

IStockDao

	package com.bjpowernode.Dao;
	
	public interface IStockDao {
	
		void insertStock(String sname, int amount);
	
		void updateStock(String sname, int amount, boolean isBuy);
	
	}

StockDaoImpl

	package com.bjpowernode.Dao;
	
	import org.springframework.jdbc.core.support.JdbcDaoSupport;
	
	public class StockDaoImpl extends JdbcDaoSupport implements IStockDao {
	
		@Override
		public void insertStock(String sname, int amount) {
			String sql = "insert into stock(sname, count) values(?,?)";
			this.getJdbcTemplate().update(sql, sname, amount);
		}
	
		@Override
		public void updateStock(String sname, int amount, boolean isBuy) {
			String sql = "update stock set count = count-? where sname=?";
			if(isBuy) {
				sql = "update stock set count = count+? where sname=?";			
			}
			this.getJdbcTemplate().update(sql, amount, sname);
		}
	
	}

#### 10.7 定义测试类

MyTest.java

	package com.bjpowernode.test;
	
	import java.nio.channels.AcceptPendingException;
	import java.util.List;
	
	import org.junit.Before;
	import org.junit.Test;
	import org.springframework.cache.support.AbstractCacheManager;
	import org.springframework.context.ApplicationContext;
	import org.springframework.context.support.ClassPathXmlApplicationContext;
	import org.springframework.dao.support.DaoSupport;
	
	import com.bjpowernode.service.BuyStockException;
	import com.bjpowernode.service.IBuyStockService;
	
	
	public class MyTest {
		
		private IBuyStockService service;
	
		@Before
		public void before() {
			String source = "applicationContext.xml";
			ApplicationContext ac = new ClassPathXmlApplicationContext(source);
			service = (IBuyStockService) ac.getBean("buyStockService");
		}
		
		@Test
		public void test01() {
			service.openAccount("张三", 10000);
			service.openStock("动力节点", 0);
		}	
		
		@Test
		public void test02() {
			try {
				service.buyStock("张三", 2000, "动力节点", 5);
			} catch (BuyStockException e) {
				e.printStackTrace();
			}
		}	
		
	}

#### 10.8 异常类

	package com.bjpowernode.service;
	
	public class BuyStockException extends Exception {
	
		public BuyStockException() {
			super();
		}
	
		public BuyStockException(String message) {
			super(message);
		}
		
	}

#### 10.9 注册

applicationContext.xml

	<?xml version="1.0" encoding="utf-8"?>
	
	<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context.xsd
	http://www.springframework.org/schema/aop
	http://www.springframework.org/schema/aop/spring-aop.xsd
	http://www.springframework.org/schema/tx
	http://www.springframework.org/schema/tx/spring-tx.xsd">
		
		<!-- 注册属性文件：从属性文件读取DB连接四要素：方式二 -->
		<context:property-placeholder location="classpath:jdbc.properties"/>
		
		<!-- 注册数据源:Spring内置的连接池 -->
		<bean id="myDataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
			<property name="driverClassName" value="${jdbc.driver}"></property>
			<property name="url" value="${jdbc.url}"></property>
			<property name="username" value="${jdbc.user}"></property>
			<property name="password" value="${jdbc.password}"></property>
		</bean>
		
		<!-- 注册dao -->
		<bean id="accountDao" class="com.bjpowernode.Dao.AccountDaoImpl">
			<property name="dataSource" ref="myDataSource"></property>
		</bean>
		
		<bean id="stockDao" class="com.bjpowernode.Dao.StockDaoImpl">
			<property name="dataSource" ref="myDataSource"></property>
		</bean>
		
		<!-- 注册service -->
		<bean id="buyStockService" class="com.bjpowernode.service.BuyStockServiceImpl">
			<property name="adao" ref="accountDao"></property>
			<property name="sdao" ref="stockDao"></property>
		</bean>
	
	<!-- ============================ AOP ===================================================  -->
		
		<!-- 注册事务管理器 -->
		<bean id="myTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
			<property name="dataSource" ref="myDataSource"></property>
		</bean>
		
		<!-- 注册事务通知 -->
		<tx:advice id="txAdvice" transaction-manager="myTransactionManager">
			<tx:attributes>
				<!-- 这里指定的是：为每一个连接点指定所要应用的事务属性 -->
				<tx:method name="open*" isolation="DEFAULT" propagation="REQUIRED"/>
				<tx:method name="buyStock" isolation="DEFAULT" propagation="REQUIRED" rollback-for="BuyStockException"/>
			</tx:attributes>
		</tx:advice>
		
		<aop:config>
			<!-- 这里指定的是切入点 -->
			<aop:pointcut expression="execution(* *..service.*.buyStock(..))" id="myPointcut"/>
			<aop:advisor advice-ref="txAdvice" pointcut-ref="myPointcut"/>	
		</aop:config>
		
	</beans>
