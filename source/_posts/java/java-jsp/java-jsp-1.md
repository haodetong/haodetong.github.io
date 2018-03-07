---
title: 第1章 JSP基础
date: 2018-01-31 19:27:10
tags: jsp
category: java
keywords: java, jsp
description: java jsp

---

### 第1章 JSP基础

#### 1.1 JSP

JSP文件的本质是Servlet。只不过，JSP与Servlet不同的是，JSP是专门用于进行数据展示的Servlet，其有特殊的写法。而普通的Servlet是用于完成业务逻辑处理的。由于Servlet是运行在单例多线程环境下的，所以JSP同样也是运行在单例多线程环境下的。

#### 1.2 JSP规范

将JSP页面翻译为Servlet的过程，是由Tomcat完成的。在Tomcat中内置了一个JSP的翻译引擎，当第一次访问该JSP页面时，翻译引擎会将JSP页面翻译为Servlet的.java文件，再将其编译为.class文件进行运行。

SUN公司制定的JavaEE规范中包含两个很重要的子规范，Servlet规范和JSP规范。其中，JSP规范中就包含了如何将JSP页面翻译为Servlet。例如，JSP页面中的html、css、javascript、普通文件部分，均会被翻译到out.write()中。

#### 1.3 JSP注释

JSP注释：`<%-- --%>`

HTML注释：`<!-- -->`

JSP注释与HTML注释区别：

（1）、HTML注释会被JSP翻译引擎翻译到Servlet的out.write()中；而JSP注释会被翻译引擎忽略，在Servlet中看不到。

（2）、客户端浏览器查看源码时，HTML注释是可以查看到的；JSP注释查看不到。

#### 1.4 JSP的java代码块

`<% %>` java代码块出现在Servlet的_jspService()方法中，不能定义方法，不能定义静态语句块。

	<html>
		<% double b = 2; %>
		<body>
			<% int a = 1; %>
		</body>
	</html>

#### 1.5 JSP的声明语句块

`<%!  %>` 声明语句块中的内容，将出现在Servlet类体中，没有包含到哪个方法体中。有线程安全隐患

	<%! 
		private int amount = 3;
		
		public void showData(){
			syso("aaaaa");
		}
	 %>

#### 1.6 JSP的表达式块

`<%= %>` 表达式块出现的_jspService()方法的out.write()方法中。

	count = <%=count %>
	
	//Servlet
	out.write("count = ");
	out.write(count);