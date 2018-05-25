---
title: 第三章 springmvc注解式开发
date: 2018-05-23 15:39:01
tags: springmvc
category: springmvc

---

#### 3.1 第一个程序

##### 3.1.1 注解

springmvc.xml
	
	<!-- 组件扫描器 -->
	<context:component-scan base-package="com.bjpowernode.handlers"></context:component-scan>
	
控制器

	@org.springframework.stereotype.Controller
	public class MyController {
		@RequestMapping("/my.do")
		public ModelAndView doSome(HttpServletRequest arg0, HttpServletResponse arg1) {
			return new ModelAndView("/WEB-INF/jsp/welcome.jsp");
		}
		
	}

##### 3.1.2 多个请求指向同一个处理器、一个处理器中定义多个处理器方法

	@org.springframework.stereotype.Controller
	public class MyController {
		@RequestMapping({"/first.do","hello.do"})
		public ModelAndView doFirst(HttpServletRequest arg0, HttpServletResponse arg1) {
			return new ModelAndView("/WEB-INF/jsp/welcome.jsp");
		}
		
		@RequestMapping("/second.do")
		public ModelAndView doSecond(HttpServletRequest arg0, HttpServletResponse arg1) {
			return new ModelAndView("/WEB-INF/jsp/welcome.jsp");
		}
	}

##### 3.1.3 命名空间 @RequestMapping

	@org.springframework.stereotype.Controller
	@RequestMapping("xxx/ooo/jjj")
	public class MyController {
		@RequestMapping({"/first.do","hello.do"})
		public ModelAndView doFirst(HttpServletRequest arg0, HttpServletResponse arg1) {
			return new ModelAndView("/WEB-INF/jsp/welcome.jsp");
		}
		
		@RequestMapping("/second.do")
		public ModelAndView doSecond(HttpServletRequest arg0, HttpServletResponse arg1) {
			return new ModelAndView("/WEB-INF/jsp/welcome.jsp");
		}
	}

##### 3.1.4 请求中的通配符

	@RequestMapping("/second*.do")

##### 3.1.5 对请求提交方式的定义

	@RequestMapping(value={"/first.do","hello.do"}, method= RequestMethod.POST)

#### 3.2 处理器方法的参数

处理器方法常用的参数有五类，这些参数会在系统调用时由系统自动赋值，即程序员可以在方法内直接使用。

*	HttpServleRequest

*	HttpServletResponse

*	HttpSession

*	用于承载数据的Model

*	请求中所携带的参数

#### 3.3 处理器方法接收请求里的参数

index.jsp

	<form action="${pageContext.request.contextPath }/test/register.do" method="POST">
		姓名：<input type="text" name="name" /><br />
		年龄：<input type="text" name="age" /><br />
		<input type="submit" value="注册" />
	</form>

welcome.jsp

	name=${name}
	age=${age }

web.xml

添加过滤器，解决中文乱码问题

	<filter>
	  	<filter-name>CharacterEncodingFilter</filter-name>
	  	<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
	  	<init-param>
	  		<param-name>encoding</param-name>
	  		<param-value>utf-8</param-value>
	  	</init-param>
	  	<init-param>
	  		<param-name>forceEncoding</param-name>
	  		<param-value>true</param-value>
	  	</init-param>
	</filter>
	  
	<filter-mapping>
		<filter-name>CharacterEncodingFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
 

##### 3.3.1 接收请求参数 - 逐个接收

控制器

	@org.springframework.stereotype.Controller
	@RequestMapping("/test")
	public class MyController {
		@RequestMapping(value={"/register.do"}, method= RequestMethod.POST)
		public ModelAndView doRegister(String name, int age) {
			System.out.println("name="+name);
			System.out.println("age="+age);
			
			ModelAndView mv = new ModelAndView();
			mv.addObject("name", name);
			mv.addObject("age", age);
			mv.setViewName("/WEB-INF/jsp/welcome.jsp");
			return mv;
		}
		
	}

##### 3.3.2 接收请求参数 - 校正请求参数名

参数pname

	姓名：<input type="text" name="pname" /><br />

@RequestParam校正

	public ModelAndView doRegister(@RequestParam("pname") String name, int age) {...}

##### 3.3.3 接收请求参数 - 以对象形式整体接收

Student.class

	public class Student {
		private String name;
		private int age;
		
		...
		getter and setter
		...
		
	}


控制器

	@org.springframework.stereotype.Controller
	@RequestMapping("/test")
	public class MyController {
		@RequestMapping(value={"/register.do"}, method= RequestMethod.POST)
		public ModelAndView doRegister(Student student) {
			System.out.println("name="+student.getName());
			System.out.println("age="+student.getAge());
			
			ModelAndView mv = new ModelAndView();
			mv.addObject("name", student);
			mv.setViewName("/WEB-INF/jsp/welcome.jsp");
			return mv;
		}
		
	}

整体接收，要求参数名name,age要与实体类属性名相同 

##### 3.3.3 接收请求参数 - 域属性参数的接收

Student.class

	public class Student {
		private String name;
		private int age;
		private School school;
		...
			getter and setter
		...
	}

School.class

	public class School {
		private String sname;
		private String address;
		public String getSname() {
			return sname;
		}
		...
			getter and setter
		...		
	}

index.jsp

	<form action="${pageContext.request.contextPath }/test/register.do" method="POST">
		姓名：<input type="text" name="name" /><br />
		年龄：<input type="text" name="age" /><br />
		学校：<input type="text" name="School.sname" /><br />
		校址：<input type="text" name="School.address" /><br />
		<input type="submit" value="注册" />
	</form>

#### 3.4 处理器方法的返回值

##### 3.4.1 返回ModelAndView

当处理器方法处理完后，既需要跳转到其它资源，也需要在资源间传递数据，那么就应该使用ModelAndView。

	@org.springframework.stereotype.Controller
	@RequestMapping("/test")
	public class MyController {
		@RequestMapping(value={"/register.do"}, method= RequestMethod.POST)
		public ModelAndView doRegister(Student student) {
			ModelAndView mv = new ModelAndView();
			mv.addObject("name", student);
			mv.setViewName("/WEB-INF/jsp/welcome.jsp");
			return mv;
		}
		
	}

##### 3.4.2 返回String

只需要进行跳转，而不需要进行数据传递时，可以使用String。

	@org.springframework.stereotype.Controller
	@RequestMapping("/test")
	public class MyController {
		@RequestMapping(value={"/register.do"}, method= RequestMethod.POST)
		public String soSome() {
			return "/WEB-INF/jsp/welcome.jsp";
		}
		
	}

物理视图：/WEB-INF/jsp/welcome.jsp

逻辑视图：welcome，需要视图解析器转换为物理视图

##### 3.4.3 返回void

index.jsp

	<script type="text/javascript">
		$(function (){
			$("button").click(function(){
				$.ajax({
					url:"test/ajax.do",
					data:{
						name:"zhangsan",
						age:23
					},
					success:function(data){
						var json=eval("("+ data +")");
						alert(json.name + json.age);
					}
				});
			});
		});
	</script>
	<button>ajax submit</button>

处理器

	@org.springframework.stereotype.Controller
	@RequestMapping("/test")
	public class MyController {
		@RequestMapping("/ajax.do")
		public void doAjax(String name, int age, HttpServletResponse response) throws IOException {
			HashMap<String, Object> map = new HashMap<String, Object>();
			map.put("name", name);
			map.put("age", age);
			
			JSONObject json = JSONObject.fromObject(map);
			String jsonStr = json.toString();
			
			PrintWriter out = response.getWriter();
			out.println(jsonStr);
		}
		
	}

需要导入json-lib相关的包

##### 3.4.4 返回Object

处理器方法也可以返回Object对象，但返回的这个Object对象不是作为逻辑视图出现的，而是作为直接在页面显示的数据出现的。

返回Object对象，需要使用@ResponseBody注解，将转换后的JSON数据放入到响应体中。

#####（1）、环境搭建

A、导入jar包

由于返回Object数据，一般都是将数据转化为了JSON对象后传递给浏览器页面的。而这个由Object转换为JSON，是由Jackson工具完成的。所以需要导入Jackson的相关jar包。

jackson-annotations、jackson-core、jackson-databind

B、注册注解驱动

将Object数据转化为JSON数据，需要由http消息转换器HttpMessageConvert完成。而消息转换器的开启，需要由`<mvc:annotation-driven>`来完成。

当Spring容器进行初始化过程中在`<mvc:annotation-driven>`处创建注解驱动时，默认创建了七个HttpMessageConvert对象。也就是说，我们注册`<mvc:annotation-driven>`，就是为了让容器为我们创建HttpMessageConvert对象。

C、@ResponseBody

给处理器方法添加注解

##### （2）、返回Object-数值型

springmvc.xml

	<?xml version="1.0" encoding="utf-8"?>
	
	<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context.xsd
	http://www.springframework.org/schema/aop
	http://www.springframework.org/schema/aop/spring-aop.xsd
	http://www.springframework.org/schema/tx
	http://www.springframework.org/schema/tx/spring-tx.xsd
	http://www.springframework.org/schema/mvc
	http://www.springframework.org/schema/mvc/spring-mvc.xsd">
		
		<!-- 注册组件扫描器 -->
		<context:component-scan base-package="com.bjpowernode.handlers"></context:component-scan>
		
		<!-- 注册注解驱动器 -->
		<mvc:annotation-driven></mvc:annotation-driven>
		
	</beans>

处理器

	@org.springframework.stereotype.Controller
	@RequestMapping("/test")
	public class MyController {
	
		@RequestMapping("/ajax.do")
		@ResponseBody //将要返回的数据放入到响应体中
		public Object doAjax() {
			return 123.321;
		}
		
	}

index.jsp

	<script type="text/javascript">
		$(function (){
			$("button").click(function(){
				$.ajax({
					url:"test/ajax.do",
					success:function(data){
						alert(data);
					}
				});
			});
		});
	</script>
	 <button>ajax submit</button>

##### （3）、返回Object-字符串

	@org.springframework.stereotype.Controller
	@RequestMapping("/test")
	public class MyController {
	
		@RequestMapping(value="/ajax.do",produces="text/html;charset=utf-8")
		@ResponseBody //将要返回的数据放入到响应体中
		public Object doAjax() {
			return "临沂";
		}
		
	}

produces指定返回的字符串格式

##### （4）、返回Object-自定义对象

处理器

	@org.springframework.stereotype.Controller
	@RequestMapping("/test")
	public class MyController {
	
		@RequestMapping("/ajax.do")
		@ResponseBody //将要返回的数据放入到响应体中
		public Object doAjax() {
			return new Student("zhangsan",23);
		}
		
	}

index.jsp

	<script type="text/javascript">
		$(function (){
			$("button").click(function(){
				$.ajax({
					url:"test/ajax.do",
					success:function(data){
						alert(data.name + data.age);
					}
				});
			});
		});
	</script>
	<button>ajax submit</button>

##### （4）、返回Object-List集合

处理器

	@org.springframework.stereotype.Controller
	@RequestMapping("/test")
	public class MyController {
	
		@RequestMapping("/ajax.do")
		@ResponseBody //将要返回的数据放入到响应体中
		public Object doAjax() {
			ArrayList<Student> list = new ArrayList<Student>();
			list.add(new Student("zhangsan",23));
			list.add(new Student("lisi",23));
			return list;
		}
		
	}

index.jsp

	<script type="text/javascript">
		$(function (){
			$("button").click(function(){
				$.ajax({
					url:"test/ajax.do",
					success:function(data){
						$(data).each(function(index){
							alert(data[index].name + data[index].age);
						});
					}
				});
			});
		});
	</script>
	 <button>ajax submit</button>
	 
##### （5）、返回Object-Map集合

处理器

	@org.springframework.stereotype.Controller
	@RequestMapping("/test")
	public class MyController {
	
		@RequestMapping("/ajax.do")
		@ResponseBody //将要返回的数据放入到响应体中
		public Object doAjax() {
			HashMap<String, Student> map = new HashMap<String,Student>();
			map.put("stu1", new Student("zhangsan",23));
			map.put("stu2", new Student("lisi",24));
			return map;
		}
		
	}
	
index.jsp

	<script type="text/javascript">
		$(function (){
			$("button").click(function(){
				$.ajax({
					url:"test/ajax.do",
					success:function(data){
						$(data).each(function(index){
							alert(data.stu1.name + data.stu2.age);
						});
					}
				});
			});
		});
	</script>
	<button>ajax submit</button>	