---
title: 第八章 Spring与AOP AspectJ基于XML的AOP实现
date: 2018-05-06 11:05:27
tags: spring
category: spring

---

#### AspectJ基于XML的AOP实现

切面

	//切面
	public class MyAspect {
		
		public void myBefore() {
			System.out.println("执行前置方法");
		}
		
		public void myBefore(JoinPoint jp) {
			System.out.println("执行前置方法jp="+jp);
		}
		
		public void myAfterReturning(String result) {
			System.out.println("执行后置方法 result="+result);
		}
		
		public Object myAround(ProceedingJoinPoint pjp) throws Throwable {
			System.out.println("执行环绕通知方法，目标方法执行之前");
			//目标方法
			Object result = pjp.proceed();
			System.out.println("执行环绕通知方法，目标方法执行之后");
			if(result != null) {
				result = ((String)result).toUpperCase();
			}
			return result;
		}
		
		public void myAfterThrowing(UsernameException ex) {
			System.out.println("发生用户名异常:"+ex.getMessage());
		}
	
		public void myAfterThrowing(PasswordException ex) {
			System.out.println("发生密码异常:"+ex.getMessage());
		}
	
		public void myAfterThrowing(Exception ex) {
			System.out.println("发生其它异常:"+ex.getMessage());
		}
		
		public void myAfter() {
			System.out.println("执行最终通知");
		}
		
		
	}

配置文件
	
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xmlns:aop="http://www.springframework.org/schema/aop" xsi:schemaLocation="
	        http://www.springframework.org/schema/beans 
	        http://www.springframework.org/schema/beans/spring-beans.xsd
	        http://www.springframework.org/schema/aop 
	        http://www.springframework.org/schema/aop/spring-aop.xsd"> 
	        
	        <!-- 注册切面 -->
	        <bean id="myAspect" class="com.siyantu.xml.MyAspect"></bean>
	        
	         <!-- 注册实现类 -->
	        <bean id="someService" class="com.siyantu.xml.SomeServiceImpl" />
	        
	        <!-- AOP配置 -->
		    <aop:config>
		    		<!-- 定义切入点 -->
		    		<aop:pointcut expression="execution(* *..ISomeService.doFirst(..))" id="doFirstPointcut"/>
		    		<aop:pointcut expression="execution(* *..ISomeService.doSecond(..))" id="doSecondPointcut"/>
		    		<aop:pointcut expression="execution(* *..ISomeService.doThird(..))" id="doThirdPointcut"/>
		    		
		    		<aop:aspect ref="myAspect">
		    			<!-- 前置通知 -->
		    			<aop:before method="myBefore" pointcut-ref="doFirstPointcut" />
		    			<!-- 指定参数类型 -->
		    			<aop:before method="myBefore(org.aspectj.lang.JoinPoint)" pointcut-ref="doFirstPointcut" />
		    			
		    			<!-- 后置通知 -->
		    			<aop:after-returning method="myAfterReturning" pointcut-ref="doSecondPointcut" returning="result" />
		    			
		    			<!-- 环绕通知 -->
		    			<aop:around method="myAround(org.aspectj.lang.ProceedingJoinPoint)" pointcut-ref="doSecondPointcut" />
		    			
		    			<!-- 异常通知 -->
		    			<aop:after-throwing method="myAfterThrowing(com.siyantu.xml.UsernameException)" pointcut-ref="doThirdPointcut" throwing="ex" />
		    			<aop:after-throwing method="myAfterThrowing(com.siyantu.xml.PasswordException)" pointcut-ref="doThirdPointcut" throwing="ex" />
		    			<aop:after-throwing method="myAfterThrowing(java.lang.Exception)" pointcut-ref="doThirdPointcut" throwing="ex" />
		    			
		    			<!-- 最终通知 -->
		    			<aop:after method="myAfter" pointcut-ref="doThirdPointcut" />
		    			
		    		</aop:aspect>
		    
		    </aop:config>
	        
	</beans>
