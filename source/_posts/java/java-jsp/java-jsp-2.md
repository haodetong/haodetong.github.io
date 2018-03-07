---
title: 第2章 JSP核心
date: 2018-01-31 19:54:50
tags: jsp
category: java
keywords: java, jsp
description: java jsp
---

### 第2章 JSP核心

### 2.1 内置对象

#### 2.1.1 pageContext

javax.servlet.jsp.PageContext

页面上下文，具有一个只在当前页面范围的域属性空间，即其具有setAttribute()方法和getAttribute()方法。

EL表达式，访问变量时，只能访问域属性空间内的变量。因此，可以将变量先存放到该域属性空间，或其它三个域属性空间（request、session、application）。

pageContext具有一些get方法，可以获取Request、Response、Session、ServletContext、ServletConfig、page(即当前Servlet)、exception、out等其它八个内置对象

#### 2.1.2 application

即ServletContext

ServletContext所具有的方法，application都有。

常用的方法，例如：

void setAttribute(String name, Object object);

Object getAttribute(String name);

Object removeAttribute(String name);

#### 2.1.3 out

所属类型：javax.servlet.jsp.JspWriter

JspWriter类继承自IO流的Writer类。

即out就是一个输出流。

#### 2.1.4 page

查看jsp翻译的servlet，page对象即Servlet对象本身。

final java.lang.Object page = this.

#### 2.1.5 exception

普通的jsp页面中，不能使用exception内置对象。因为打开jsp翻译的servlet，发现其中并没有exception对象。

若要在页面中直接使用exception对象，则需要配合着page指令使用。

#### 2.1.6 其它对象

request,response,session,config，用法与servlet时的用法相同。

### 2.2 JSP指令(directive)

jsp中包含三类指令：

##### page指令

##### include指令

##### taglib标签库指令

<%@ 指令名称 属性名 = 属性值 属性名 = 属性值 %>

#### 2.1.1 page指令

（1）、pageEncodeing属性

<%@ page pageEncoding = "utf-8"%>

默认的MIME类型为text/html

//servlet
response.setContentType("text/html;charset=UTF-8");

(2)、contentType属性

<%@ page contentType = "text/html; charset=UTF-8" %>

//servlet
response.setContentType("text/html;charset=UTF-8");

可以对pageEncoding默认的MIMe类型进行修改

（3）、import属性

<%@ page import = "java.util.Date"%>

(4)、errorPage\isErrorPage属性

	<%@ page errorPage = "/error.jsp" %>

//error.jsp
	
	<%@ page isErrorPage = "true"%>
	<html>
		<body>
			error page<br />
			ex = <%=exception.getMessage() %>
		</body>
	</html>
	
//serlvet

	public void _jspService(...){
		...
		java.lang.Throwable exception = ...;
		...
	}
	
(5)、Session属性

//hello1.jsp

	<body>
		<%
			session.setAttribute("user","zhangsan");
		%>
	</body>

//servlet
	
	session = pageContext.getSession();
	
	查看源码发现，返回的还是request.getSession()
	
1、	设置

HttpSession session = request.getSession(true);
session.setAttribute(...);

设为true，意味着没有session对象时才创建，有则不创建。

2、读取

HttpSession session = request.getSession(false);
if(session != null){
	session.getAttribute("...");
}

设为false，意味着获取现有的session对象，如果没有，也不创建。

//hello2.jsp

	<%@ page contentType = "text/html; charset = UTF-8"%>
	<%-- 清除内置的Session对象 --%>
	<%@ page session = "fasle"%>
	
	<body>
		HttpSession session = 	request.getSession(false);
		if(session != null){
			String user = (String) session.getAttribute("user");
			out.print("user = " + user);
		}		
	<body/>

#### 2.2.2 include指令

(1)、用法

可以包含动态文件也可以包含静态页面文件。

index.jsp
	
	<html>
	<body>
		index page before<br />
		
		<%@ include file = "/next.jsp"%>
		
		index page after<br />
	</body>
	</html>

next.jsp

	<html>
	<body>
		next page
	</body>
	</html>

（2）、静态联编

只生成了一个index_jsp.java的servlet源文件，没有生成next_jsp.java文件。

jsp翻译引擎，将include指令包含的next.jsp文件直接翻译到了index.jsp对应的servlet中，形成了一个.java文件。

该包含操作是在编译之前，由jsp翻译引擎完成的，而不是在程序运行期间完成的。

这种包含是一种静态包含，称为静态联编。

整个过程只一个_jspService()方法。也就是说，index.jsp和next.jsp之间是可以相互访问局部变量的。

### 2.3 JSP动作（Action）

页面中，应该使用EL表达式、JSTL标签及JSP动作，来代替java代码块、表达式。

JSP动作的语法格式

`<jsp:动作名称 属性名 = 属性值 属性名 = 属性值 ...></jsp:动作名称>`

或

`<jsp:动作名称 属性名 = 属性值 属性名 = 属性值 ... />`

实际开发中，常用的就两个forward和include。

底层使用的是`request.getRequestDispatcher("").forward(...)和.include()`。

#### 2.3.1 forward动作

index.jsp

	<body>
		index page befor <br />
		
		<jsp:forward page="/next.jsp"></jsp:forward>
		
		index page after <br />
		
	<body/>
	
next.jsp

	<body>
		next page
	</body>

forward跳转时，只显示

	next page
	
#### 2.3.2 include动作

index.jsp

	<body>
		index page befor <br />
		
		<jsp:include page="/next.jsp"></jsp:include>
		
		index page after <br />
		
	<body/>
	
next.jsp

	<body>
		next page
	</body>


include跳转时，全显示
	
	index page befor
	next page
	index page after 

(注)：

生成了两个.java文件：index_jsp.java与left_jsp.java。

这个包含动作是在程序运行过程中，由index_jsp文件中的_jspService()方法通过JspRuntimeLibrary类的include()方法调用了left_jsp文件中的_jspService()方法完成的。

这种在运行期执行的包含，称为动态联编。	


### 2.4 EL表达式

Expression Language，表达式语言。

${expression}获取到指定表达式的值。

#### 2.4.1 获取数据

(1)、EL只能从四大域中获取数据

其查找数据的顺序是，依次按照由小到大的范围，从四大域中查找指定名称的属性值，找到后就不再继续往下查找。

	<body>
		<%
			String username = "abc";
			pageContext.setAttribute("username", username);
			request.setAttribute("username", username);
			session.setAttribute("username", username);
			application.setAttribute("username", username);
		%>
		//从四大域属性空间中依次查找
		username = ${username}
		
	</body>

（2）、从指定域中获取数据
	
	//通过EL的内置对象，从指定的域属性空间中查找
	username = ${requestScope.username}

（3）、访问Bean的属性

	<body>
		<%
			School school = new School("qinghua","beijing");		
			Student studentb = new Student("zhangsan",23);
			pageContext.setAttribute("student", student);
		%>
		
		student = ${student}
		
		name = ${student.name}
		
		schoolName = ${student.school.sname}
		
	</body>

（4）、获取数组中的元素

	<body>
		
		<%
			String[] names = {"zhangsan", "lisi", "wangwu"};
			pageContext.setAttribute("names", names);
		%>
		
		names[1] = ${names[1]}
		
		<%
			School[] schools = new School[3];
			schools[0] = new School("qinghua", "beijing");
			schools[1] = new School("haiyang", "qingdao");
			schools[2] = new School("shanda", "shandong");
			pageContext.setAttribute("chools", schools);
		%>
		
		chools[2].sname = ${schools[2].sname}
				
	</body>

（5）、获取List中的元素
	
	EL可以通过索引访问List，但无法访问Set。因为Set中没有索引的概念。
	
	<body>
		<%
			List<String> names = new ArrayList<String>();
			names.add("zhangsan");
			names.add("lisi");
			names.add("wangwu");
			pageContext.setAttribute("names",names);
		%>
		
		names[2] = ${names[2]};
		
	<body/>

（6）、获取Map中的元素

	<body>
		<%
			Map<String, Object> map = new Map<>();
			map.put("school", new School("qinghua", "beijing"));
			map.put("mobile", "1111111");
			map.put("age", 21);
			pageContext.setAttribute("map", map);
		%>
		
		school.name = ${map.school.sname}
		
		mobile = ${map.mobile};
		
		age = ${map.age}；
		
	</body>

#### 2.4.2 运算符

算术运算符，关系运算符，逻辑运算符，条件运算符，取值运算符

除了这些，还有一个empty

${empty 变量}

* 变量未定义，返回true
* 变量为String类型，空串返回true
* 变量为引用类型，其值为null返回true
* 变量为集合类型，不包含任何元素，返回true

#### 2.4.3 EL内置对象
EL中表示四个域属性空间的内置对象：pageScope requestScope sessionScope applicationScope

其它的常用内置对象还有：

（1）、pageContext
	
EL中的pageContext与JSP中内置对象中的pageContext是同一个对象。

通过该对象，可以获取request、response、session、servletContext、servletConfig等对象。

*	`${pageContext.request.contextPath}`，获取项目根目录，一般用在JSP页面的路径前。

	<form action = "${pageContext.request.contextPath/register.do}" method = "post">
		...
	</form>
	
*	pageContext.request 其底层实际调用的是pageContext.getRequest()方法
	
*	在EL的11个内置对象中，除了pageContext外，其它10个内置对象，其类型均为java.util.Map类型。	

（2）、param

获取请求中指定参数的值

GET: http:localhost:8080/textproject/index.jsp?name=abc

//index.jsp

param.name = ${param.name}

POST:

	<form action = "${pageContext.request.contextPath/show.jsp}">
		姓名：<input type = "text" name = "name" />
		年龄：<input type = "text" name = "age" />
		爱好：
		<input type = "checkbox" name = "hobby" value = "swimming" />
		<input type = "checkbox" name = "hobby" value = "climbing" />
		<input type = "checkbox" name = "hobby" value = "reading" />
	</form>

//show.jsp

	name = ${param.name}
	age = ${param.age}
	

（3）、paramValues
	
	hobby[0] = ${paramValues.hobby[0]}
	hobby[1] = ${paramValues.hobby[0]}
	hobby[2] = ${paramValues.hobby[0]}
	
（4）、initParam
	
获取web.xml中初始化参数

web.xml

	<context-param>
		<param-name>company<param-name/>
		<param-value>powernode<param-value/>
	</context-param>
	
jsp页面

	company = ${initParam.company}


#### 2.4.4 自定义EL函数

自定义EL函数，实际字符串连接的功能

1、定义类
	
	public class ELFunctions{
		public static String lowerToUpper(String source){
			return source.toUpperCase();
		}
		
		public static String upperToLower(String source){
			return source.toLowerCase();
		}
	}
	
2、标签库定义 注册

自定义的类及其函数，需要在扩展名为.tld的XML文件中进行注册

XML文件是需要约束的，即需要配置文件头部。这个头部约束可以从以下文件中复制。

comcat-9/webapps/examples/web-inf/jsp2/jsp2-example-taglib.tld

//WEB-INF/myElFuns.tld

	<tablib ...>
		
		//定义标签库信息
		<tlib-version>1.0</tlib-version>
		<show-name>myElFuncs</short-name>
		<uri>http://www.test.com/myproject/jsp/el/customElFuncs</uri>
		
		//注册函数
		<function>
			<name>myLowerToUpper</name>
			<function-class>myproject.testpackage. ELFunctions</function-class>
			<function-signature>java.lang.String. lowerToUpper(java.lang.String)</function-signature>
		<function/>
		
		<function>
			<name>myUpperToLower</name>
			<function-class>myproject.testpackage. ELFunctions</function-class>
			<function-signature>java.lang.String. upperToLower(java.lang.String)</function-signature>
		<function/>
		
	</tablib>

//index.jsp

	<%@ taglib uri = "http://www.test.com/myproject/jsp/el/customElFuncs" prefix = "myElFuncs"%>
	<body>
		${myElFuncs:myLowerToUpper("abc")}
	</body>
	

#### 2.4.5 JSTL中的EL函数

JSTL，JSP Standard Tag Library，即JSP标准标签库。

该标签库中，定义好了一套处理字符串的函数标签库。

将JSTL的Jar包导入WEb/lib后，在jsp页面中即可直接使用。

//index.jsp

	<%@ taglib uri = "http://java.sun.com/jsp/jstl/functions" prefix = "fn"%>
	
	<body>
		${fun.substring("abcdefg", 2, 5)}
		.....
		.....
	</body>

#### 2.4.6 EL总结

*	EL不能出现在java代码块、表达式块等jsp动态代码部分
* 	EL只能从pageContext、request、session、application四大域属性空间中获取数据
*  EL不会抛出空指针异常
*  EL不会抛出数组访问越界异常
*  EL不具有字符串处理能力，只能通过JSTL标签库来处理

### 2.5 自定义标签

简化代码，替代java代码块。

#### 2.5.1 基本用法

（1）、需求

自定义标签，输出客户端Ip

（2）、定义标签处理器

实现处理器接口：javax.servlet.jsp.tagext.simpleTag

	//定义标签处理器
	public class ClientIpTag extends SimpleTagSupport {
		@Override
		public void doTag() throws JspException, IOException {
			//获取pageContext
			PageContext pc = (PageContext) this.getJspContext();
			//获取请求对象
			ServletRequest request = pc.getRequest();
			//获取客户端IP
			String clientIp = request.getRemoteAddr();
			//获取标准输出流
			JspWriter out = pc.getOut();
			//输出
			out.print(clientIp);
		}
	}

（3）、注册标签处理器

//customTags.tld

	<?xml version="1.0" encoding="UTF-8"?>
	<taglib xmlns="http://java.sun.com/xml/ns/j2ee"
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-jsptaglibrary_2_0.xsd"
	    version="2.0">
	    <!--配置标签库信息-->
	    <tlib-version>1.0</tlib-version>
	    <short-name>customTags</short-name>
	    <uri>http://www.jstlproject.com/jsp/tags/custom</uri>
	    <!--注册标签-->
	    <tag>
	        <name>clientIp</name>
	        <tag-class>tags.ClientIpTag</tag-class>
	        <body-content>empty</body-content>
	    </tag>
	    
	</taglib>

（4）、使用自定义标签
	
	<%@ taglib uri = "http://www.jstlproject.com/jsp/tags/custom" prefix = "customTags" %>
	<body>
		<% 
			String ip = request.getRemoteAddr();
			out.println("ip = " + ip);
		%>
		<br/>
		<customTags:clientIp />
	</body>

#### 2.5.2 定义带标签体的标签

//标签处理器 - 小写变大写
	
	public class LowerToUpperTag extends SimpleTagSupport {
		@Override
		public void doTag() throws JspException, IOException {
			//获取标签体对象
			JspFragment jspbody = this.getJspBody();
			//创建字符串输出流
			StringWriter strWriter = new StringWriter();
			//标签体对象写入字符串输出流
			jspbody.invoke(strWriter);
			//字符串输出流中的数据
			String str = strWriter.toString();
			//小写变大写
			str = str.toUpperCase();
			//写入标准输出流
			this.getJspContext().getOut().print(str);
		}
	}

注册

	<?xml version="1.0" encoding="UTF-8"?>
	<taglib xmlns="http://java.sun.com/xml/ns/j2ee"
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-jsptaglibrary_2_0.xsd"
	    version="2.0">
	    <!--配置标签库信息-->
	    <tlib-version>1.0</tlib-version>
	    <short-name>customTags</short-name>
	    <uri>http://www.jstlproject.com/jsp/tags/custom</uri>
	    <!--注册标签-->
	    <tag>
	        <name>clientIp</name>
	        <tag-class>tags.ClientIpTag</tag-class>
	        <body-content>empty</body-content>
	    </tag>
	    <tag>
	        <name>lowerToUpper</name>
	        <tag-class>tags.LowerToUpperTag</tag-class>
	        <body-content>scriptless</body-content>
	    </tag>
	    
	</taglib>
	
附：<body-content></body-content>

*	empty:表示当前标签没有标签体
* 	scriptless：表示当前标签具有标签体，对el表达式解析。
*  jsp:原样输出，已过时
*  tagdependent: 原样输出到浏览器，不解析el表达式
	
//index.jsp

	<%@ taglib uri = "http://www.jstlproject.com/jsp/tags/custom" prefix = "customTags" %>

	<body>
		
		<%
			String username = "aaa";
			pageContext.setAttribute("username", username);
		%>
		<customTags:lowerToUpper>${username }</customTags:lowerToUpper>
		
	</body>

#### 2.5.3 定义带属性的标签

//注册标签

	<tag>
        <name>if</name>
        <tag-class>tags.IfTag</tag-class>
        <body-content>tagdependent</body-content>
    		<attribute>
    			<name>test</name>
    			<required>true</required>
    			<!-- runtime expression value 
    				true:该属性的值支持el表达式与jsp表达式
    			-->
    			<rtexprvalue>true</rtexprvalue>
    		</attribute>
    </tag>

//定义标签处理器

	public class IfTag extends SimpleTagSupport {
		private boolean test;
		//标签的属性反映到标签处理器中，就是一个set属性
		public void setTest(boolean test) {
			this.test = test;
		}
		@Override
		public void doTag() throws JspException, IOException {
			if(test) {
				/*
				JspFragment jspbody = this.getJspBody();
				jspbody.invoke(this.getJspContext().getOut());
				*/
				//以上代码等价于以下代码
				this.getJspBody().invoke(null);
			}
		}
	}

//index.jsp

	<customTages:if test = ${gender} }} }>男</customTages:if>
	<customTages:if test = not gender }>女</customTages:if>

#### 2.5.4 定义forEach标签

//定义标签处理器，遍历collection集合和数组

	public class ForEachTag extends SimpleTagSupport {
		
		private Object items;
		private String var;
		
		public void setItems(Object items) {
			this.items = items;
		}	
		
		public void setVar(String var) {
			this.var = var;
		}
		
		public Collection getcoll() {
			if(items instanceof List) {
				return (List) items;
			}else if(items instanceof Set) {
				return (Set) items;
			}else if(items instanceof Map) {
				return ((Map) items).entrySet();
			}else if(items instanceof Object[]) {
				return Arrays.asList((Object[])items);
			}
			return null;
		}
		
		@Override
		public void doTag() throws JspException, IOException {
			for (Object obj:getcoll()) {
				this.getJspContext().setAttribute(var, obj);
				this.getJspBody().invoke(null);
			}
		}
		
	}

//注册

 	<tag>
        <name>forEach</name>
        <tag-class>tags.ForEachTag</tag-class>
        <body-content>scriptless</body-content>
    		<attribute>
    			<name>items</name>
    			<required>true</required>
    			<rtexprvalue>true</rtexprvalue>
    		</attribute>
    		<attribute>
    			<name>var</name>
    			<required>true</required>
    			<rtexprvalue>false</rtexprvalue>
    		</attribute>
    </tag>

//index.jsp

	<customTags:forEach items="${citys}" var="city">
		${city }<br>
	</customTags:forEach>

#### 2.5.5 将自定义标签库打包发行

1、右键export选择jar包，只打包源码。

2、把.tld注册文件，放到jar包里的META-INFO里面。

3、把jar包放到项目的WEB-INF的lib下。

4、jsp页面中导入标签库，就可以直接用了。

### 2.6 JSTL

jsp standard tag library，包含五个子库。

核心标签库、格式化标签库、EL函数标签库、SQL操作标签库（过时）、XML操作标签库（过时）。

导入jstl.jar包和standard.jar包


#### 2.6.1 核心标签库

<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>

（1）、c:set
	
	//将变量存放到指定域中
	<c:set var="name" value="zhangsan" scope="session">
	name = ${sessionScope.name}
	
	//为Bean的属性赋值
	<%
		Student student = new Student();
		pageContext.setAttribute("student", student);
	%>
	<c:set value="lisi" property="name" target="${pageScope.student}"></c:set>
	student = ${student}
	
	//为map赋值
	<%
		Map<String, Object> map = new HashMap<>();
		pageContext.setAttribute("map", map);
	%>
	<c:set value="abc" property="company" target="${pageScope.map}"></c:set>
	map = ${map}
	
（2）、c:remove
	
	//从域属性空间中删除变量
	<c:remove var="school">

（3）、c:out

	//输出指定的变量
	<c:set var="city" value="<h1>beijing</h1>">
	
	city1 = <c:out value="${city}"/>
	
	<br />
	
	city2 = <c:out value="${city}" escapeXml="false" />
	
	city1对h1标签进行了解析。
	
	city2对h1标签没有进行解析，直接输出。

（4）、c:catch
	
	//获取异常信息
	<c:catch var="ex">
		<%
			int i = 3/0;
		%>
	</c:catch>
	
（5）、c:if
	
	<c:if test="${user == "zhangsan"}">
		<a href="#">进入系统</a>
	</c:if>
	
（6）、c:choose
	
	<c:choose>
		<c:when test="${pagenumber == 1}">
			第一页
		</c:when>
		<c:when test="${pagenumber == 2}">
			第二页
		</c:when>
		<c:otherwise>
			第三页
		</c:otherwise>
	</c:choose>

（7）、c:forEach
	
	//遍历集合
	<c:forEach items="${citys}" var="city" begin="0" end="7" step="4">
		${city} <br>
	</c:forEach>

	//输出行号
	<c:forEach items="${students}" var="student" varStatus="vs">
		<tr class="${vs.count/2 == 0 ? 'even' : 'odd'}">
			<td>${vs.count}</td>
			<td>${student.name}</td>
			<td>${student.age}</td>
		</tr>
	</c:forEach>
	
#### 2.6.1 格式化标签库

<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt" %>

	<%
		Date now = new Date();
		pageContext.setAttribute("now", now);
	%>
	
	//格式化日期，输出到浏览器
	now = <fmt:formatDate value="${now}" pattern="yyyy-MM-dd">
	
	//格式化日期，将结果放到域中的变量里
	<fmt:formatDate value="${now}" pattern="yyyy-MM-dd" var="birth">
	生日：<input type="text" name="birthday" value="${birth}" />
	
#### 2.6.2 jstl下载
	
apache.org --> Tomcat --> Taglibs --> Download

