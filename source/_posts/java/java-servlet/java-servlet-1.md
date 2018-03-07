---
title: java Servlet 一、基础
date: 2018-01-25 20:30:10
tags: servlet
category: java
keywords: java, servlet
description: java servlet

---

创建工程时：

1、选择Dynamic Web Project

2、Dynamic Web Module version 选择2.5

### 一、Servlet基础

#### 1.1 Servlet生命周期

#### 1.1.1生命周期方法执行流程

实例化 ---> 初始化 ---> 服务 ---> 销毁

Servlet的整个生命周期过程的执行，均由web服务器负责管理。即Servlet从创建到服务到销毁的整个过程中方法的调用，都是由web服务器调用执行，程序员无法控制其执行流程。

但程序员可以获取到Servlet的这些生命周期的时间点，并可以指定让Servlet做一些具体业务相关的事情。

实现Servlet接口，重写五个方法

	public class SomServlet implements Servlet {
	
		public void init(ServletConfig config) throws ServletException {
			// TODO Auto-generated method stub
		}
	
		public void destroy() {
			// TODO Auto-generated method stub
		}
	
		public ServletConfig getServletConfig() {
			// TODO Auto-generated method stub
			return null;
		}
	
		public String getServletInfo() {
			// TODO Auto-generated method stub
			return null; 
		}
	
		public void service(ServletRequest request, ServletResponse response) throws ServletException, IOException {
			// TODO Auto-generated method stub
		}
	
	}

对于以上代码的执行，注意以下几个时间点：

（1）、项目部署后启动服务器发现并没有执行Servlet的无参构造器，说明在Web容器启动时并没有创建Servlet对象

（2）、用户提交请求后，马上可以看到无参构造器、init()方法、service()方法均执行。

（3）、刷新页面，发现只会执行service()方法，每刷新一次，即每提交一次请求，就会执行一次service()方法。

（4）、让另外一个浏览器也发现同样的请求，会发现只执行service()方法，而无参构造器、init()方法均未执行。

（5）、正常关闭Tomcat(使用右键stop server关闭，不能使用Terminate关闭)，发现destroy()方法也会执行。

#### 1.1.2 Servlet特征

（1）、Servlet是单例多线程的

（2）、一个Servlet实例只会执行一次无参构造器方法与init()方法，并且是在第一次访问时执行。

（3）、每提交一次对当前Servlet的请求，就会执行一次service()方法

（4）、一个Servlet实例只会执行一次destroy()方法，在应用停止时执行。

（5）、由于Servlet是单例多线程的，为了保证其线程的安全性，一般情况下是不为Servlet类定义可修改的成员变量的。因为每个线程均可修改这个成员变量，会出现线程安全问题。

（6）、默认情况下，Servlet在Web容器启动时是不会被实例化的。

#### 1.1.3 Web容器启动时创建Servlet实例

Servlet注册时，添加<load-on-startup> int x </load-on-startup>，x大于等于0，值越小，越优先被实例化。

	  <servlet>
	    <servlet-name>SomServlet</servlet-name>
	    <servlet-class>servlet01.SomServlet</servlet-class>
	    <load-on-startup>0</load-on-startup>
	  </servlet>
	  <servlet-mapping>
	    <servlet-name>SomServlet</servlet-name>
	    <url-pattern>/SomServlet</url-pattern>
	  </servlet-mapping>
	  
#### 1.1.4 Servlet中的两个Map

服务器中存在有两个Map

Map-one 

key ------------------ value

url-pattern ----------- Servlet引用

提交请求,解析url，获取uri，查找map-one中的key，找到后，运行该Servlet的service()方法

Map-two

key ---------------- value

url-pattern --------- <servlet-class>

如果map-one中找不到对应的url-pattern，就会查找map-two，找到对应注册的servlet-class名，应用反射机制，创建该servlet实例，
把实例的引用放到map-one中，执行service()方法

#### 1.2、ServletConfig

#### 1.2.1 什么是ServletConfig

其实就是封装的注册中的servlet

	 <servlet>
	    <description></description>
	    <display-name>SomServlet</display-name>
	    <servlet-name>SomServlet</servlet-name>
	    <servlet-class>servlet01.SomServlet</servlet-class>
	  </servlet>

#### 1.2.2 获取ServletConfig

	public class SomServlet implements Servlet {
		private ServletConfig config;
		public void init(ServletConfig config) throws ServletException {
			this.config = config;
			System.out.println(config);
		}
	
		public void destroy() {
		}
	
		public ServletConfig getServletConfig() {
			return null;
		}
	
		public String getServletInfo() {
			return null; 
		}
	
		public void service(ServletRequest request, ServletResponse response) throws ServletException, IOException {
		}
	
	}

tomcat服务器会通过init方法将ServletConfig对象传给servlet类

#### 1.2.3 ServletConfig中的方法

public String getServletName();

public ServletContext getServletContext();

public String getInitParameter(String name);

public Enumeration<String> getInitParameterNames();

##### web.xml

		<servlet>
	    <description></description>
	    <display-name>SomServlet</display-name>
	    <servlet-name>SomServlet</servlet-name>
	    <servlet-class>servlet01.SomServlet</servlet-class>
	    <init-param>
	    		<param-name>adress</param-name>
	    		<param-value>beijing</param-value>
	    </init-param>
	    <init-param>
	    		<param-name>phone</param-name>
	    		<param-value>15022222222</param-value>
	    </init-param>
	  </servlet>
	  <servlet-mapping>
	    <servlet-name>SomServlet</servlet-name>
	    <url-pattern>/SomServlet</url-pattern>
	  </servlet-mapping>

##### SomServlet.class

	public class SomServlet implements Servlet {
		private ServletConfig config;
		public void init(ServletConfig config) throws ServletException {
			this.config = config;
			System.out.println("servletconfig:"+config);
		}
	
		public void destroy() {
		}
	
		public ServletConfig getServletConfig() {
			return null;
		}
	
		public String getServletInfo() {
			return null; 
		}
	
		public void service(ServletRequest request, ServletResponse response) throws ServletException, IOException {
			//获取servlet名称
			String servletName = config.getServletName();
			System.out.println("servletName:" + servletName);
			
			//获取servletcontext对象
			ServletContext  servletContext = config.getServletContext();
			System.out.println("ServletContext:" + servletContext);
			
			//获取该servlet里边的所有的初始化参数
			Enumeration<String> names = config.getInitParameterNames();
			while(names.hasMoreElements()) {
				String name = names.nextElement();
				String value = config.getInitParameter(name);
				System.out.println(name + "=" + value);
			}
		}
	
	}

##### 输出结果：

	servletconfig:org.apache.catalina.core.StandardWrapperFacade@79bfdb2d
	servletName:SomServlet
	ServletContext:org.apache.catalina.core.ApplicationContextFacade@e11d81f
	phone=15022222222
	adress=beijing

#### 1.3 ServletContext

代表整个应用，一个应用只有一个ServletContext


返回应用的名称

	String getContextPath()

获取所有的servlet共享的初始化参数

	Enumeration<String> getInitParameterNames();
	String getInitParameter(String name)

获取真正的路径
 
 	String getRealPath(String path)

域属性空间：存放所有的servlet可以共享的属性

设置域属性，可以实现在一个servlet中设置，另一个servlet中获取
	
	setAttribute(key,vlaue)
	getAttribute(key)
	removeAttribute(key)

#### 1.4、设置欢迎页面

#### 1.5、<url-pattern/>设置与匹配

#### 1.5.1 <url-pattern>设置

（1）、精确路径模式

	<servlet-mapping>
		<servlet-name>some-servlet</servlet-name>
		<url-pattern>/some</url-pattern>
		<url-pattern>/test/other</url-pattern>
		<url-pattern>/aaa/bbb/ccc</url-pattern>
	<servlet-mapping/>

（2）、通配符路径模式
	
	<url-pattern>/some/*</url-pattern>

（3）、全路径模式
	
	/*与/都是全路径模式，但他们还是不同的。
	
	/*是真正的全路径模式，可以拦截所有请求，无论是动态资源还是表态资源请求，均会被拦截（service方法）。
	
	/只会拦截静态资源请求	

（4）、后辍名模式
	
	*.do

#### 1.5.2 注意事项
	
后辍名模式的用法限制

	带/的通配符路径模式与后辍名模式不能同时使用。例如：不能使用/*.do，/xxx/*.do

#### 1.5.3 匹配原则

（1）、路径优先后辍

	例如SomServlet的<url-patten>为*.do，OtherServlet的<url-pattern>为/xx/*，请求提交的url为：http://localhost:8080/oa/xxx/abc.do，同时匹配两个url-pattern时，会按照路径优先于后辍的匹配原则，选择OtherServlet。
	
（2）、精确路径优先

	http://localhost:8080/oa/some	请求 同时匹配/some与/*时，优先配置/some

（3）、最长路径优先

	http://localhost:8080/oa/some/other 请求 同时匹配/some/*和/some/other/*。优先选择/some/other对应的servlet
	
	
	
	