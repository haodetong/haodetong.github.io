---
title: java Servlet 二、核心
date: 2018-01-25 20:30:10
tags: servlet
category: java
keywords: java, servlet
description: java servlet

---

### 二、Servlet核心

#### 2.1 GenericServlet

#### 2.1.1 GenericServlet 源码
 
	//缺省适配器设计模式
	public abstract class GenericServlet implements Servlet, ServletConfig,
	        java.io.Serializable {
	
	    private transient ServletConfig config;
	
	    @Override
	    public void destroy() {
	        // NOOP by default
	    }

	    @Override
	    public String getInitParameter(String name) {
	        return getServletConfig().getInitParameter(name);
	    }

	    @Override
	    public Enumeration<String> getInitParameterNames() {
	        return getServletConfig().getInitParameterNames();
	    }
	
	    @Override
	    public ServletConfig getServletConfig() {
	        return config;
	    }
	
	   
	    @Override
	    public ServletContext getServletContext() {
	        return getServletConfig().getServletContext();
	    }
	
	   
	    @Override
	    public String getServletInfo() {
	        return "";
	    }
		
		//模板设计模式
	    public void init(ServletConfig config) throws ServletException {
	        this.config = config;
	        //调用无参的init()方法
	        this.init();
	    }
	
	   	//该无参的init()方法，就是要让子类来重写的
	    public void init() throws ServletException {
	        // NOOP by default
	    }
	
	    //抽象化service方法，让子类去实现
	    public abstract void service(ServletRequest req, ServletResponse res)
	            throws ServletException, IOException;
	
	    
	    @Override
	    public String getServletName() {
	        return config.getServletName();
	    }
	}

实现了servlet接口，并抽象化了service方法，让子类去实现。

实现了servletconfig接口，子类可以直接调用servletconfig的方法。

#### 2.1.2 获取请求的提交方式
	
	public class genericSevletDemo extends GenericServlet {
		private static final long serialVersionUID = 1L;
	       
		public void service(ServletRequest request, ServletResponse response) throws ServletException, IOException {
			HttpServletRequest req = (HttpServletRequest) request;
			HttpServletResponse res = (HttpServletResponse) response;
			String method = req.getMethod();
			
			if("POSt".equals(method)){
				doPost(req,res);
			}else if("GET".equals(method)){
				doGet(req,res);
			}
		}
	
	}

继续封装doPost和doGet为HttpServlet类

#### 2.2 HttpServlet
	
HttpServlet源码
	
	public abstract class HttpServlet extends GenericServlet {
		
		@Override
    public void service(ServletRequest req, ServletResponse res){
     	HttpServletRequest  request;
        HttpServletResponse response;

        try {
            request = (HttpServletRequest) req;
            response = (HttpServletResponse) res;
        } catch (ClassCastException e) {
            throw new ServletException("non-HTTP request or response");
        }
        service(request, response);
    }
		
		 protected void doGet(HttpServletRequest req, HttpServletResponse resp)
	        throws ServletException, IOException {
		    	...
		    }
	    
	    protected void doPost(HttpServletRequest req, HttpServletResponse resp)
	        throws ServletException, IOException {
	        ...
	        }
    } 
	

//servlet继承httpservlet类

	
	public class HttpservDemo extends HttpServlet {
		private static final long serialVersionUID = 1L;
	
		protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			response.getWriter().append("Served at: ").append(request.getContextPath());
		}
	
		protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			doGet(request, response);
		}
	
	}

#### 2.3 HttpServletRequest

##### 2.3.1 请求的生命周期

当客户端浏览器将请求发送到服务器后，服务器会根据HTTP请求协议的格式对请求进行解析。同时，服务器会创建HttpServletRequest的实现类RequestFacade的对象，即请求对象。然后再调用相应的set方法，将解析出的数据封装到请示对象中。此时HttpServletRequest实例就创建并初始化完成了。也就是说，请求对象是由服务器创建的的。

当服务器向客户端发送响应结束后，HttpServletRequest实例对象就被服务器销毁。

一次请求对应一个请求对象，别外一次请求对应另一个请求对象，与之前的请求对象没有任何关系。HttpServletRequest实例的生命周期很短暂。

##### 2.3.2 请求参数

HttpServletRequest对于请求中所携带的参数是以Map的形式接收的，并且该Map的key为String，value为String数组（一个请求参数可能会有多个值的情况出现）。

	public class HttpservDemo extends HttpServlet {
		private static final long serialVersionUID = 1L;
	
		protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			//getParameter()方法，获取指定key对应的value，等同于getParameterValues()[0]
			String name = request.getParameter("name");
			String ageStr = request.getParameter("age");
			Integer age = Integer.valueOf(ageStr);
			System.out.println("name = "+name);
			System.out.println("age = "+age);
			
			//getParameterName()方法,获取所有keys
			Enumeration<String> names = request.getParameterNames();
			while(names.hasMoreElements()) {
				String eleName = names.nextElement();
				String eleValue = request.getParameter(eleName);
				System.out.println(eleName + "-----" + eleValue);
			}
			
			//getParameterValues()方法,获取指定key对应的所有values，
			//如<checkbox name = "hobby">running</checkbox>
			//如<checkbox name = "hobby">reading</checkbox>
			String[] hobby = request.getParameterValues("hobby");
			for(String h : hobby) {
				System.out.println(h);
			}
			
			//getParameterMap()方法,获取存放请求参数的Map
			Map<String, String[]> map = request.getParameterMap();
			for(String key : map.keySet()) {
				System.out.println(key+"-----"+request.getParameter(key));
			}
		}
	
		protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			doGet(request, response);
		}
	
	}

##### 2.3.3 域属性

Request中也存在域属性空间，用于存放有名称的数据。该数据只在当前Request请求中可以访问。

对于Request中的域属性操作的常用方法：

设置

	void setAttribute(String name, Object object);

获取

	Object getAttribute(String name);
	
在FirstServlet中设置，跳转到otherServlet中获取
	
	public class FirstServlet extends HttpServlet {   
		protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			request.setAttribute("address", "zhuhaijinwan");
			request.setAttribute("phone", "15011112222");
			
			request.getRequestDispatcher("/other").forward(request, response);
		}
	}
	
	public class OtherServlet extends HttpServlet {
		protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			String address = (String) request.getAttribute("address");
			String phone = (String) request.getAttribute("phone");
		
			Enumeration<String> names = request.getAttributeNames();
			while(names.hasMoreElements()) {
				String name = names.nextElement();
				String value = (String) request.getAttribute(name);
			}
		}
	}


##### 2.3.4 服务器相关信息

1、获取请求的URL，如：“http://localhost:8080/httpServlet/some”

	StringBuffer getRequestURL();
	
2、获取请求的URI，如："/httpServlet/some"
	
	String getRequestURI();
	
3、获取当前应用在Web容器中的名称，如："httpServlet"

	String getContext();	

4、获取路径中与web.xml中注册servlet时的`<url-pattern/>`相匹配的路径信息
	
	//servletPath指的是与<url-pattern>精确部分相匹配的路径
	String getServletPath()
	
	//pathInfo指的是与非精确部分相匹配的路径
	String getPathInfo()
	
a、例如:`<url-pattern/>`的值为/xxx/ooo/*，用户提交的请求为：http://localhost:8080/httpServlet/xxx/ooo/abc/def。

servletPath就是：/xxx/ooo

pathInfo就是：/abc/def

b、若用户提交的请求为后辍方式，如:http://localhost:8080/httpServlet/aaa/bbb/ccc.do。

servletpath就是：/aaa/bbb/ccc.do

pathInfo: null

c、URI = ServletContext + ServletPath + PathInfo

5、获取请求的方式是post还是get等

	String getMethod()
	
6、获取客户端浏览器的IP
	
	//对于服务器端来说，客户端就是远程端
	String getRemoteAddr()

#### 2.4 中文乱码问题

当页面中提交一个包含中文的请求时，在服务器端有可能会出现中文乱码问题。

##### 2.4.1 乱码产生的原因

Http协议中规定，数据传输采用字节码编码方式，即无论浏览器提交的数据包含的中文是什么字符编码格式，一旦由浏览器经过Http传输协议传输，则这些数据均将以字节码的形式上传给服务器。

因为Http协议的底层使用的TCP（Transmission Contrl Protocol）传输协议。传输控制协议是一种面向连接的、可靠的、基于字节流的、端对端的通信协议。在请求中，这些字节均以%开头，并以十六进制形式出现。如%5A%3D等。

那么乱码是如何产生的呢？

当用户通过浏览器提交一个包含 UTF-8编码格式的两个字的中文请求时，浏览器会将这两个中文字符变为六个字节，即形成六个类似%8E的字节表示形式，并将这六个字节上传到tomcat服务器。

tomcat服务器接收到这六个字节后，并不知道它们原来采用的是什么字符编码。而tomcat默认的编码格式为ISO-8859-1。所以会将这个六个字符按照这种格式进行编码，编码后在控制台显示，所以在控制台会显示乱码。

##### 2.4.3 乱码的解决方法

1、tomcat9已经解决了GET提交时的中文乱码问题。

2、request.setCharacterEncoding("UTF-8");解决了POST提交中文时的乱码问题。无法解决GET提交时的中文乱码问题。

3、Get提交时中文乱码解决方案有两种：

（1）、修改tomcat/conf/server.xml，添加URIEncoding
	
	<Connector port="8080" protocol="HTTP/1.1"
		connectionTimeOut="20000" redirectPort="8443" URIEncoding="UTF-8"
	/>

(2)、无论Post还是get都可以

	String name = request.getParaeter("name");
	byte[] bytes = name.getBytes("ISO8859-1");
	name = new String(bytes,"UTF-8");
			

#### 2.5 HttpServletResponse

web服务器收到一个Http请求后，会针对每个请求创建一个HttpServletRequest对象和HttpServletResponse对象。若需要获取客户端提交请求的相关信息，刚需要HttpServletRequest对象来完成。若需要向客户端发送数据，则需要通过HttpServletResponse对象来完成。

##### 2.5.1 向客户端发送数据

ServletResonse接口有一个方法getWriter()，用于获取到一个输出流对象PrintWriter，该输出流对象是专门用于向客户端浏览器中输出字符数据的，称为标准输出流。

	response.getWriter().append("Served at: ").append(request.getContextPath());

	PrintWriter out = response.getWriter();
	out.append("dddd");
	out.print("aaa");
	out.pirntln("bbbb");
	out.Write("ccc");
	
##### 2.5.2 响应乱码的产生

两种方法：

1、设置MIME类型，设置字符编码格式

	response.setContentType("text/html");
	response.setCharacterEncoding("UTF-8");

2、同时设置MIME类型和字符编码格式

	response.setContentType("text/html;charset=UTF-8");
		

#### 2.6 请求转发与重定向

通过HttpServletRequest获取到的RequestDispatcher对象的forward()方法，可以完成请求转发功能。

而通过HttpServletResponse的sendRedirect()方法，可以完成重定向功能。

##### 2.6.1什么是请求转发与重定向

请求转发：一次请求，一次响应。

重定向：多次请求，多次响应。

##### 2.6.2 请求转发程序举例

	public class SomServlet extends HttpServlet {
		protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
			String name = req.getParameter("name");
			String age = req.getParameter("age");
			req.setAttribute("ageStr", age);
			//请求转发
			req.getRequestDispatcher("other").forward(req, resp);
		}
	}
	
	public class OtherServlet extends HttpServlet {
		protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			//可以继续使用request,response,也可以获取someServlet中设置的域属性
			String name = request.getParameter("name");
			request.getAttribute("ageStr");
			
			PrintWriter out = response.getWriter();
			out.print("this is otherServlet");
		}
	}

##### 2.6.3 重定向举例

	public class SomServlet extends HttpServlet {
	
		protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
			String name = req.getParameter("name");
			String age = req.getParameter("age");
			req.setAttribute("ageStr", age);
			req.getRequestDispatcher("other").forward(req, resp);
			
			//重定向
			resp.sendRedirect("other");
		}
	}
	
	public class OtherServlet extends HttpServlet {
		protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			//不能再继续使用request,response,也不能获取someServlet中设置的域属性
			PrintWriter out = response.getWriter();
			out.print("this is otherServlet");
		}
	
	}

##### 2.6.4 重定向时的数据传递

//重定向数据传递，及乱码解决方案
	
	public class SomServlet extends HttpServlet {
		protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
			String name = req.getParameter("name");
			//解决中文乱码
			name = new String(name.getBytes("ISO8859-1"),"UTF-8");
			
			String age = req.getParameter("age");
			req.setAttribute("ageStr", age);
			req.getRequestDispatcher("other").forward(req, resp);
			
			//编码，解决重定向传递时乱码问题
			name =  URLEncoder.encode(name,"UTF-8");
			
			//重定向
			resp.sendRedirect("other?pname="+name+"&page="+age);
		}
		
	}	
	
	public class OtherServlet extends HttpServlet {
		protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		
			String pname = request.getParameter("pname");
			//解码
			pname = URLDecoder.decode(pname,"UTF-8");
			
			//打散组装
			pname = new String(pname.getBytes("ISO8859-1"),"UTF-8");
			
			String page = request.getParameter("page");
			
			System.out.println("pname====" +pname);
			System.out.println("page====" +page);
			
			PrintWriter out = response.getWriter();
			out.print("this is otherServlet");
		}
	
	}

##### 2.6.5 重定向到其它应用

重定向与请求转发还有一点很重要的不同点是，重定向可以跳转到其它应用中，而请求转发只能在当前应用中跳转。也正因为如此，所以sendRedirect()的参数中，必须要添加request.getContext(),即当前应用的根目录，指定要跳转到哪个应用的哪个资源。

 	//重定向到另一个应用的名称为other的servlet
 	response.sendRedirect("/redirect-two/other");

##### 2.6.6 请求转发与重定向对比

(1)、请求转发

A、浏览器只发出一次请求，收到一次响应

B、请求所转发到的资源中可以直接获取到请求中所携带的数据

C、浏览器地址栏显示的是用户所提交的路径

D、只能跳转到当前应用中的资源

(2)、

A、浏览器发出两次请求，接收到两次响应

B、重定向到的资源不能直接获取到用户提交请求中所携带的数据

C、浏览器地址栏显示的是重定向的请求路径，而非用户提交请求的路径。也正因为如此，重定向有一个很重要的作用：防止表单重复提交

D、重定向不仅可以跳转到当前应用的其它资源，也可以跳转到其它应用中的资源

##### 2.6.7 请求转发与重定向的选择

（1）、若需跳转到其它应用，只能选择重定向

（2）、若是处理表单数据的servlet，要跳转到其它servlet，则需要选择重定向。为了防止表单重复提交。

（3）、若对某一请求进行处理的servlet的执行需要消耗大量的服务器资源（cpu,内存），此时这个servlet执行完毕后，也需要重定向。

（4）、其它情况，一般使用请求转发。

#### 2.7 RequestDispatcher  

RequestDispatcher是javax.servlet包下的一个接口，通过HttpServletRequest可以获取到其接口对象，该对象就是用于完成转发功能的。

#### 2.7.1 forward()与include()

SomeServlet

	public class SomServlet extends HttpServlet {
	
		@Override
		protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
			System.out.println("some request = " + req);
			System.out.println("some resp = " + resp);
			req.getRequestDispatcher("other").forward(req, resp);
		}
		
	}
	
OtherServlet

	public class OtherServlet extends HttpServlet {
		private static final long serialVersionUID = 1L;
	       
		protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			System.out.println("other response = " + response);
			System.out.println("other request = " + request);
		}
	
	}

输出结果中的request：

	some request = org.apache.catalina.connector.RequestFacade@289113c7
	other request = org.apache.catalina.core.ApplicationHttpRequest@4d0d88e6

1、other中的HttpServletRequest request是对some中的HttpServletRequest req的增强，实际上是将用户请求和转发请求这两次请求进行了合并。

2、将req.getRequestDispatcher("other").forward(req, resp);改为req.getRequestDispatcher("other").include(req, resp);后，输出结果和上面的相同。说明对于request，forward与include没有区别。

输出结果中的response:
	
	//forward()方法跳转时	
	some resp = org.apache.catalina.connector.ResponseFacade@6451e20e
	other response = org.apache.catalina.connector.ResponseFacade@6451e20e
	
	//include()方法跳转时
	some resp = org.apache.catalina.connector.ResponseFacade@48202e11
	other response = org.apache.catalina.core.ApplicationHttpResponse@5f53ab27

1、forward()方法跳转时，some中的resp与other中的response一样，都是ResponseFacade。

2、include()方法跳转时，some中的resp为ResponseFacade，other中的response为ApplicationHttpResponse。

3、说明requestDispatcher的forward()与include()的区别，主要集中在响应对象上。

#### 2.7.2 代码测试

SomeServlet

	public class SomServlet extends HttpServlet {
	
		@Override
		protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
			PrintWriter out = resp.getWriter();
			out.print("someServlet:forward() before ");
	
			req.getRequestDispatcher("other").forward(req, resp);
			
			out.print("someServlet:forward() after ");
		}
		
	}

OtherServlet

	public class OtherServlet extends HttpServlet {
		private static final long serialVersionUID = 1L;
	       
		protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			PrintWriter out = response.getWriter();
			out.print("other servlet data");
		}
	
	}

客户端浏览器输出结果：

forward()方法跳转的输出结果：

	other servlet data

incluce()方法跳转的输出结果：
	
	someServlet:forward() before 
	other servlet data
	someServlet:forward() after 


1、forward()与include()的区别，主要表现在标准输出流的开启时间不同。

2、说明在SomeServlet中，用forward()方法向前跳转时，请求此时还并没有结束，所以reqp还没有开启，因此两个输出语句没有执行。

3、include()方法跳转时，some和other中的输出语句全部执行。说明，include不仅将other的数据写入到了标准输出流中，也将some中的数据包含到了自己的输出流中，

4、forward的转发示意：

浏览器--->发送请求--->服务器中的SomeServlet---->发送请求---->服务器中的OtherServlet--->OtherServlet对浏览器做出响应。

向浏览器给出响应的是OtherServlet，输出流是在OtherServlet中开启的。

5、include的转发示意：

浏览器--->发送请求--->服务器中的SomeServlet---->发送请求--->服务器中的OtherServlet---->OtherServlet将SomeServlet进行了包含，并向SomeServlet做出响应 ---> SomeServle对浏览器做出响应。

向浏览器给出响应的是SomeServlet，输出流在SomeServlet中就开启了。

包含的过程就是由ApplicationHttpResponse这个增强的response来完成的。

### 2.9 Servlet的线程安全问题

Servlet是单例多线程环境下运行的，其运行可能会产生线程安全问题。

#### 2.9.1 线程安全问题

1、同时满足以下两个条件，则会出现线程安全问题。

（1）、存在多线程并发访问

（2）、存在可修改的共享数据

2、JVM中可能存在线程安全问题的数据分析

（1）、栈内存数据分析

栈内存是是多例的，即JVM会为每个线程创建一个栈，所以其中的数据是不共享的。另外，栈里面放的是方法栈帧，一个方法在栈里面，以一个栈帧的形式出现，方法的栈帧中放的是方法的签名（头部信息）、局部变量、返回的信息等，方法中的局部变量存放在Stack的栈帧中，方法执行完毕，栈帧弹栈，局部变量消失。局部变量是局部的，不是共享的。所以栈内存中的数据不存在线程安全问题。

（2）、堆内存数据分析

 一个JVM中只存放一个堆内存，堆内存是共享的。被创建出的对象存放在堆内存中，而存放在堆内存中的对象，实际就是对象成员变量的集合。即成员变量是存放在堆内存的。堆内存中的数据是多线程共享的，也就是说，堆内存中的数据是存在线程安全隐患的。
 
（3）、方法区数据分析

一个JVM中只存在一个方法区。方法的代码片段，静态变量与常量都存放在方法区，方法区是多线程共享的。常量是不能修改的，所以常量不存在安全问题。静态变量是多线程共享的，所以静态变量存在安全隐患。 

3、线程安全问题的解决方案

（1）、对于一般性的类，不要定义为单例的。除非项目有特殊需求，或该类对象属于重量级对象。所谓重量级对象是指，创建该类对象是会占用较大的系统资源。

（2）、无论类是否为单例类，尽量不要使用静态变量。

（3）、若需要定义为单例类，则单例类尽量不使用成员变量（例如Servlet类）。

（4）、若单例类中必须要使用成员变量，则对成员变量的操作添加串行化锁synchronized，实现线程同步。不过，最好不要使用线程同步机制，因为一旦操作进入串行化的排队状态，将大大降低程序的执行效率。

#### 2.9.2 Servlet的线程安全问题

Servlet是单例多线程并发访问的，所以存在线程安全问题。

为了避免问题产生，对用Servlet类的使用，一般是不声明成员变量的。

若项目中必须要求声明成员变量，则只能通过同步机制synchronize避免。


#### 2.9.3 对线程安全问题的合理利用

Servlet中的成员变量是每一个线程均可修改和访问的，所以可以利用这一点，实现计数器功能，用于统计当前Servlet的被访问的次数。
	
	public class SomeServlet extends HttpServle{
		//定义成员变量 - 计数器
		private int count;
		public void doGet(...){
			count++;
			response.setContentType("text/html;charset=UTF-8");
			PrinterWriter out = resp.getWriter();
			out.print("当前网页的访问次数：" + count);
			
		}
	}
	
	