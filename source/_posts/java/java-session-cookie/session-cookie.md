---
title: session&cookie
date: 2018-04-27 10:12:04
tags: session cookie
category: java
keywords: java, session, cookie
description: java-session-cookie

---

#### 1.1 cookie简介

cookie是1993年由网景公司前雇员发明的一种进行网络会话状态跟踪的技术。

会话是由一组请求与响应组成，是围绕着一种相关事情所进行的请求与响应。所以这些请求与响应之间一定是需要有数据传递的，即需要进行会话状态跟踪。然而HTTP协议是一种无状态协议，在不同的请求之间是无法进行数据传递的。此时就需要一种可以进行请求之间进行数据传递的会话跟踪技术，而cookie就是一种这样的技术。

cookie是由服务器生成，保存在客户端的一种信息载体。这个载体中存放着用记访问该站点的会话状态信息。只在cookie没有被清空，或者cookie没有失效，那么，保存在其中的会话状态就有效。

用户在提交第一次请求后，由服务器生成cookie，并将其封装到响应头中，以响应的形式发送给客户端。客户端接收到这个响应后，将cookie保存到客户端。当客户端再次发送同类请求后（资源路径相同，资源名称不同），在请求中会携带保存在客户端的cookie数据，发送到客户端，由服务器对会话进行跟踪。

cookie技术并不是jvavaweb开发的专属技术，而是属于web开发的技术，是所有web开发语言均支持的技术。

cookie是由若干键值对构成，键值对均为字符串。


#### 1.2 javaEE中的cookie

在javaEE中的javax.servlet.http包中存在一个类Cookie，就是用于完成会话跟踪的Cookie。其只有一个带参构造器。

##### 默认绑定路径

访问路径由资源路径与资源名称构成。默认情况下，cookie与访问路径中的资源路径绑定。只要用户发出带有绑定资源路径的请求，则在请求头部，将会自动携带与之绑定的cookie路径。

##### 生成cookie 
	
	public class SomeServlet extends HttpServlet {
		private static final long serialVersionUID = 1L;
	    
		protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			response.getWriter().append("Served at: ").append(request.getContextPath());
			//创建两个Cookie
			Cookie cookie = new Cookie("company","abc");
			Cookie cookie2 = new Cookie("teacher","def");
			
			//指定cookie绑定的资源路径。必须加上项目名称
			cookie.setPath(request.getContextPath() + "/aaa/bbb/ccc");
			cookie2.setPath(request.getContextPath() + "/ddd/eee/fff");
			
			//设置cookie有效期
			//大于0，表示将cookie存放到客户端硬盘
			//小于0，与不设置一样，会将cookie存放到浏览器的缓存
			//等于0，表示cookie一生成，马上失效
			cookie.setMaxAge(60 * 60);
			cookie2.setMaxAge(60 * 60 * 24 * 10);
			
			//向响应中添加Cookie
			response.addCookie(cookie);
			response.addCookie(cookie2);
		
		}
	}

##### 获取cookie

	public class OhterServlet extends HttpServlet {
		private static final long serialVersionUID = 1L;
		
		protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			response.getWriter().append("Served at: ").append(request.getContextPath());
			
			Cookie[] cookies = request.getCookies();
			
			for(Cookie cookie:cookies) {
				System.out.println(cookie.getName() + "===" + cookie.getValue());
				if(cookie.getName().equals("teacher") && cookie.getValue().equals("aaa")) {
					//...
				} else {
					//..
				}
			}
		}
	
	}

#### 2.1 HttpSession

 session，即会话，是web开发中的一种会话状态跟踪技术。不同的是,cookie是将会话状态保存在了客户端，而session是将会话状态保存在了服务器端。
 
 在javaweb开发中，session是以javax.servlet.HttpSession的接口对象的形式出现。

---

对于request的getSession()的用法：

*	一般情况下，若要向session域中写入数据，则需要使用getSession(true)，即getSession()方法。意味着有老的session用老的，没有老的则创建新的。

*	若要从session中读取数据，则需要使用getSession(false)。意味着有老的session用老的，没有老的返回null。

---
 
	<!DOCTYPE html>
	<html>
	<head>
	<meta charset="UTF-8">
	<title>Insert title here</title>
	</head>
	<body>
		<form action = "FirstServlet" method = "POST">
			用户名：<input type = "text" name = "username" />
			<input type = "submit" value = "提交" />
		</form>
	</body>
	</html>
 
 
 FirstServlet
 
	public class FirstServlet extends HttpServlet {
		private static final long serialVersionUID = 1L;
	       
		protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			//获取用户提交的数据
			String username = request.getParameter("username");
			
			//将参数放到request域
			request.setAttribute("user", username);
			
			//获取session对象
			HttpSession session = request.getSession();
			
			//向session域中写入数据
			session.setAttribute("username", username);
			
			response.getWriter().print("SomeServlet" + username);
			
		}
	
	}
 
SecondServlet

	public class SecondServlet extends HttpServlet {
		private static final long serialVersionUID = 1L;
	       
		protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			//从request域中读取user属性
			Object user = request.getAttribute("user");
			
			//获取session
			HttpSession session = request.getSession(false);
			
			//从session中读取指定属性
			String username = null;
			if(session != null) {
				username = (String) session.getAttribute("username");
			}
			
			PrintWriter out = response.getWriter();
			out.println("user = " + user);
			out.println("username = " + username);
			out.println("session = " + session);
		}
	
	}
 
#### 2.2 Session的工作原理

在服务器中系统会为每个会话维护一个Session。不同的会话，对应不同的Session。那么，系统是如何识别Session对象的？即是如何做到在同一个会话过程中，一直使用的是同一个Session对象呢？

##### （1）、写入Session列表

服务器对当前应用中的Session是以Map的形式进行管理的，这个Map称为Session列表。该Map的key为一个32位长度的随机串，这个随机串称为JSessionID，value则为Session对象的引用。

当用户每一次提交请求时，服务端Servlet中执行到request.getSession()方法后，会自动生成一个Map.entry对象，key为一个根据某种算法新生成的JSessionID，value则为新创建的HttpSession对象。

##### (2)、服务器生成并发送cookie

在将session信息写入session列表后，系统还会自动将“JSessionID”作为name,这个32位长度的随机串作为value，以cookie的形式存放到响应报头中，并随着响应，将该cookie发送到客户端。

http://localhost:8080/sessioncookie
提交表单，跳转到FirstServlet后，

	Response Headers
	
	Content-Length: 13
	
	Date: Fri, 27 Apr 2018 07:54:40 GMT
	
	Set-Cookie: JSESSIONID=DCCE10CC2F5A6FAE40B716CA91B01EB0; Path=/sessioncookie; HttpOnly

##### (3)、客户端接收并发送cookie

客户端接收到这个cookie后会将其存放到浏览器缓存中。即，只要客户端浏览器不关闭，浏览器缓存中的cookie就不会消失。

当用户提交第二次请求时，会将缓存中的这个cookie，伴随着请求的头部信息，一块发送到服务端。

再次发送请求时，http://localhost:8080/sessioncookie/SecondServlet

	Request Headers

	...
	
	Cookie: JSESSIONID=DCCE10CC2F5A6FAE40B716CA91B01EB0
	
	Host: localhost:8080

（4）、从session列表中查找

服务端从请求中读取到客户端发送来的cookie，并根据cookie的SeesionID的值，从Map中查找相应key所对应的value，即session对象。然后，该session的域属性进行读写操作。

#### 2.3 Session的失效

若某个session在指定的时间内一直未被访问，那么session将超时，即将失效。

在web.xml中可以通过`<session-config />`标签设置session的超时时间，单位为分钟。默认的超时时间为30分钟。时间从最后一次访问开始计时。

	<session-config>
		<session-timeout>120</session-timeout>
	</session-config>

若未到超时时间，也可以通过HttpSession中的方法invalide()，使得session失效。

#### 2.4 cookie禁用后的session

cookie禁用后，从someServlet跳转到otherServlet时，

	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			//获取用户提交的数据
			String username = request.getParameter("username");
			
			//获取session对象
			HttpSession session = request.getSession();
			
			//向session域中写入数据
			session.setAttribute("username", username);
			
			//跳转到otherServlet
			response.sendRedirect(request.getContextPath() + "/otherServlet");	
		}

someServlet发送到客户端的包含有sessionid的cookie并没有被客户端保存，所以在发送otherServlet请求时，没有将该包含有sessionid的cookie发送到服务端，因此otherServlet服务端找不到该session，也就不能读取里面的数据。

##### 解决方法：

request.encodeRedirectURL()方法

	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		//获取用户提交的数据
		String username = request.getParameter("username");
		
		//获取session对象
		HttpSession session = request.getSession();
		
		//向session域中写入数据
		session.setAttribute("username", username);
		
		//跳转到otherServlet
		String uri = request.getContextPath() + "/otherServlet";
		uri = request.encodeRedirectURL(uri);
		response.sendRedirect(uri);	
	}

这样的话，跳转到otherServlet时，会将sessionid包含到访问地址中。

	http://localhost/8080/sessioncookie/otherServlet;jsessionid=6BD1D909077DF0798ASSSJAK8200EC12
	
这样的话，发送请求到OtherServlet时，就会将该sessionid发送到服务端。

#### 2.5 cookie禁用后非重定向跳转时session的跟踪
	
HttpServletResponse具有一个方法encdodeURL()，可以完成类似超链接这样的非重定向页面跳转的URL的重写，即在其路径后自动添加jsessionid
	
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		//获取用户提交的数据
		String username = request.getParameter("username");
		
		//获取session对象
		HttpSession session = request.getSession();
		
		//向session域中写入数据
		session.setAttribute("username", username);
		
		//跳转到otherServlet
		response.setContentType("text/html;charset=utf-8");
		PrintWriter out = response.getWriter();
		String uri = "otherServlet";
		uri = response.encodeURL(uri);
		out.println("<a href='" + uri + "'>跳转</a>到otherservlet");
	}
