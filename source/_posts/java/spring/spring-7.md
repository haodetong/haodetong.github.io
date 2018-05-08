---
title: 第七章 Spring与AOP AspectJ基于注解的AOP实现
date: 2018-05-06 11:01:39
tags: spring
category: spring

---

#### 7.1 AspectJ 基于注解的AOP实现 - 前置通知

指定ISomeService的doFirst()接入点为切入点，并执行前置方法

主业务接口

	public interface ISomeService {
		void doFirst();
		String doSecond(String str);
		void doThird();
	}

业务实现类

	public class SomeServiceImpl implements ISomeService {
		@Override
		public void doFirst() {
			System.out.println("执行doFirst()方法");
		}
		@Override
		public String doSecond(String str) {
			System.out.println("执行doSecond()方法");
			return str;
		}
		@Override
		public void doThird() {
			System.out.println("执行doThird()方法");
		}
	}

切面

	@Aspect //表示当前类为切面
	public class MyAspect {
		//设置前置方法及切入点
		@Before("execution(* *..ISomeService.doFirst(..))")
		public void myBefore(JoinPoint jp) {
			System.out.println("执行前置方法jp="+jp);
		}
	}

配置文件

	<!-- 注册切面 -->
    <bean id="myAspect" class="com.siyantu.annotation.MyAspect"></bean>
    
     <!-- 注册实现类 -->
    <bean id="someService" class="com.siyantu.annotation.SomeServiceImpl" />
    
    <!-- aspectj自动代理 -->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
 
测试类
	
	public class MyTest {
		@Test
		public void test() {
			String resource = "applicationContext.xml";
			ApplicationContext ac = new ClassPathXmlApplicationContext(resource);
			ISomeService service = (ISomeService) ac.getBean("someService");
			service.doFirst();
			System.out.println("=============");
			service.doSecond("abcde");
			System.out.println("=============");
			service.doThird();
			System.out.println("=============");
			
		}
		
	}
 
#### 7.2 AspectJ 基于注解的AOP实现 - 后置通知 

	@Aspect //表示当前类为切面
	public class MyAspect {
		
		...
		
		//设置后置方法及切入点，可以获取目标方法结果，但是因为没有返回值，所以没法修改
		@AfterReturning(value="execution(* *..ISomeService.doSecond(..))",returning="result")
		public void myAfterReturning(String result) {
			System.out.println("执行后置方法 result="+result);
		}
		
	}

执行结果：

	执行前置方法jp=execution(void com.siyantu.annotation.ISomeService.doFirst())
	执行doFirst()方法
	=============
	执行doSecond()方法
	执行后置方法 result=abcde
	=============
	执行doThird()方法
	=============

#### 7.3 AspectJ 基于注解的AOP实现 - 环绕通知


	@Aspect //表示当前类为切面
	public class MyAspect {
		
		...
		//设置环绕通知及切入点
		@Around("execution(* *..ISomeService.doSecond(..))")
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
		
	}
	
执行结果：
	
	执行前置方法jp=execution(void com.siyantu.annotation.ISomeService.doFirst())
	执行doFirst()方法
	=============
	执行环绕通知方法，目标方法执行之前
	执行doSecond()方法
	执行环绕通知方法，目标方法执行之后
	执行后置方法 result=ABCDE
	=============
	执行doThird()方法
	=============


#### 7.4 AspectJ 基于注解的AOP实现 - 异常通知

##### 异常分两类：

1、运行时异常，不进行处理，也可以通过编译。

若一个类继承自RunTimeException，则该异常就是运行时异常。

2、编译时异常，受查异常，Checked Exception。不进行处理，将无法通过编译。

若一个类继承自Exception，则该类就是受查异常。

##### 自定义异常

主业务接口

	public interface ISomeService {
		void doThird(String username, String password) throws UserException;
	}

业务实现类

	public class SomeServiceImpl implements ISomeService {
		@Override
		public void doThird(String username, String password) throws UserException {
			if(!"beijing".equals(username)) {
				throw new UsernameException("用户名有误");
			}
			if(!"111".equals(password)) {
				throw new PasswordException("密码有误");
			}
		}
	}

切面

	@Aspect //表示当前类为切面
	public class MyAspect {
		
		//当目标方法抛出与指定类型的异常具有is-a关系的异常时，执行该异常通知方法	
		@AfterThrowing(value="execution(* *..ISomeService.doThird(..))",throwing="ex")
		public void myAfterThrowing(UsernameException ex) {
			System.out.println("发生用户名异常:"+ex.getMessage());
		}
	
		@AfterThrowing(value="execution(* *..ISomeService.doThird(..))",throwing="ex")
		public void myAfterThrowing(PasswordException ex) {
			System.out.println("发生密码异常:"+ex.getMessage());
		}
	
		@AfterThrowing(value="execution(* *..ISomeService.doThird(..))",throwing="ex")
		public void myAfterThrowing(Exception ex) {
			System.out.println("发生其它异常:"+ex.getMessage());
		}
		
	}

自定义UserException

	public class UserException extends Exception {
		public UserException() {
			super();
		}
		public UserException(String message) {
			super(message);
		}
	}

自定义UsernameException

	public class UsernameException extends UserException {
		public UsernameException() {
			super();
			// TODO Auto-generated constructor stub
		}
		public UsernameException(String message) {
			super(message);
			// TODO Auto-generated constructor stub
		}
	}

测试类
	
	异常处理方式一：将异常继续向上抛出，抛给虚拟机
	
	public class MyTest {
		@Test
		public void test() throws UserException {
			String resource = "applicationContext.xml";
			ApplicationContext ac = new ClassPathXmlApplicationContext(resource);
			ISomeService service = (ISomeService) ac.getBean("someService");
			
			service.doThird("beijing","1112");
			
		}
		
	}

	异常处理方式二：捕捉异常，自己处理

	public class MyTest {
		@Test
		public void test() {
			String resource = "applicationContext.xml";
			ApplicationContext ac = new ClassPathXmlApplicationContext(resource);
			ISomeService service = (ISomeService) ac.getBean("someService");
			
			try {
				service.doThird("beijing","1112");
			} catch (UserException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			
		}
		
	}

#### 7.5 AspectJ 基于注解的AOP实现 - 最终通知

切面

	@Aspect //表示当前类为切面
	public class MyAspect {
		
		//最终通知
		@After("execution(* *..ISomeService.doThird(..))")
		public void myAfter() {
			System.out.println("执行最终通知");
		}
		
	}

不管有没有异常，最终通知都会执行

#### 7.6 AspectJ 基于注解的AOP实现 - 定义切入点

	@Aspect //表示当前类为切面
	public class MyAspect {
		
		//最终通知，使用定义的切入点替代表达式
		@After("doThirdPointcut()")
		public void myAfter() {
			System.out.println("执行最终通知");
		}
		
		//定义切入点
		@Pointcut("execution(* *..ISomeService.doThird(..))")
		public void doThirdPointcut() {}
		
	}

