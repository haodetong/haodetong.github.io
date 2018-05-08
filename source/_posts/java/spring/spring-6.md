---
title: 第六章 Spring与AOP AspectJ对AOP的实现
date: 2018-05-05 14:34:38
tags: spring
category: spring

---

Spring与AspectJ都对AOP这种编程思想进行了实现，但AspectJ实现方式更为简捷方便，而且还支持注解式开发。所以Spring又将AspectJ对于AOP的实现引入到了自己的框架中。

#### 6.1 AspectJ的通知类型

	（1）、前置通知
	
	（2）、后置通知
	
	（3）、环绕通知
	
	（4）、异常通知
	
	（5）、最终通知

#### 6.2 AspectJ的切入点表达式

表达式原型：

execution(
	[modifiers-pattern] 访问权限类型
	ret-type-apttern 返回值类型
	[declaring-type-pattern] 全限定性类名
	name-pattern(param-pattern) 方法名（参数名）
	[throws-pattern] 抛出异常类型
)

其中，返回值类型与方法名（参数名）不能省略。

切入点表达式要匹配的对象就是目标方法的方法名。

*	`*` ：代表0到多个任意字符

*	`..`：用在方法参数中，表示任意多个参数；用在包名中表示当前包及其子包路径

*	`+`：用在类名中，表示当前类及其子类；用在接口中表示当前接口及其实现类

`execution(* *..service.*.*(..))`

表示指定所有包下的service子包下所有类或接口中所有方法为切入点

#### 6.3 测试环境的搭建

#####（1）、导入jar包

AOP的jar包，AOP联盟的jar包，AspectJ的jar包，spring与AspectJ的整合jar包。

#####（2）、引入AOP的约束



