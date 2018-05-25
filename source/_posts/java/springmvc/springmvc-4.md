---
title: 第四章 springmvc核心技术
date: 2018-05-24 15:59:14
tags: springmvc
category: springmvc

---

#### 4.1 请求转发与重定向

当处理器对请求处理完毕后，向其它资源进行跳转时，有两种跳转方式：请求转发与重定向。而根据所要跳转的资源类型，又可分为两类：跳转到其它页面与跳转到其它处理器。

注意，对于请求转发的页面，可以是WEB-INF中的页面；而重定向的页面，不能是WEB-INF中的页面。因为重定向相当于用户再次发出一次请求，而用户是不能直接访问WEB-INF中的资源的。

##### 4.1.1 返回ModelAndView时的请求转发

（1）、请求转发到页面

（2）、请求转发到Controller

##### 4.1.2 返回ModelAndView时的重定向

（1）、重定向到页面

A、通过ModelAndView的Model携带参数

index.jsp

	<form action="${pageContext.request.contextPath }/test/register.do" method="POST">
		姓名：<input type="text" name="name" /><br />
		年龄：<input type="text" name="age" /><br />
		<input type="submit" value="注册" />
	</form>

处理器

	@org.springframework.stereotype.Controller
	@RequestMapping("/test")
	public class MyController {
		
		@RequestMapping("/register.do")
		public ModelAndView doSecond(String name, int age) {
			ModelAndView mv = new ModelAndView();
			mv.addObject("name", name);
			mv.addObject("age", age);
			mv.setViewName("redirect:/welcome.jsp");
			return mv;
		}
	}

跳转到welcome.jsp页面获取接收参数
	
	<!-- 
	param.name底层执行的是request.getParameter("name") 
	requestScope.name底层执行的是request.getAttribute("name")
	-->
	name = ${param.name}
	<br/>
	age = ${param.age}

B、通过HttpSession携带参数

（2）、重定向到Controller

A、通过ModelAndView的Model携带参数

B、通过HttpSession携带参数

##### 4.1.3 返回String时的请求转发

（1）、请求转发到页面

（2）、请求转发到Controller

##### 4.1.4 返回String时的重定向

（1）、重定向到页面

（2）、重定向到Controller

index.jsp

	<form action="${pageContext.request.contextPath }/test/register.do" method="POST">
		姓名：<input type="text" name="name" /><br />
		年龄：<input type="text" name="age" /><br />
		<input type="submit" value="注册" />
	</form>

处理器	
	
	@org.springframework.stereotype.Controller
	@RequestMapping("/test")
	public class MyController {
		
		@RequestMapping("/register.do")
		public String doRegister(String name, int age, Model model) {
			model.addAttribute("name",name);
			model.addAttribute("age",age);
			
			return "redirect:other.do";
		}
		
		@RequestMapping("/other.do")
		public String doOther(String name,int age) {
			System.out.println("name = " + name);
			System.out.println("age = " + age);
			return "/welcome.jsp";
		}
	}

跳转到welcome.jsp页面获取接收参数
	
	<!-- 
	param.name底层执行的是request.getParameter("name") 
	requestScope.name底层执行的是request.getAttribute("name")
	-->
	name = ${param.name}
	<br/>
	age = ${param.age}

##### 4.1.5 返回void时的请求转发

（1）、请求转发到页面

（2）、请求转发到Controller

##### 4.1.6 返回void时的重定向

（1）、重定向到页面

（2）、重定向到Controller


#### 4.2 异常处理

 常用的springmvc异常处理方式主要有三种：
 
 *	使用系统定义好的异常处理器SimpleMappingExceptionResolver

 *	使用自定义异常处理器

 *	使用异常处理注解

##### 4.2.1 SimpleMappingExceptionResolver异常处理器

springmvc.xml

	<context:component-scan base-package="com.bjpowernode.handlers"></context:component-scan>
	
	<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
		<property name="defaultErrorView" value="/errors/error.jsp"></property>
		<property name="exceptionAttribute" value="ex"></property>
		<property name="exceptionMappings">
			<props>
				<prop key="com.bjpowernode.exceptions.NameException">/errors/nameError.jsp</prop>
				<prop key="com.bjpowernode.exceptions.AgeException">/errors/ageError.jsp</prop>
			</props>
		</property>
	</bean>
	
StudentException.class

	public class StudentException extends Exception {
	
		public StudentException() {
			super();
		}
	
		public StudentException(String message) {
			super(message);
		}
	
	}

NameException.class

	public class NameException extends StudentException {
	
		public NameException() {
			super();
		}
	
		public NameException(String message) {
			super(message);
		}
	
	}

AgeException.class

	public class AgeException extends StudentException {
	
		public AgeException() {
			super();
		}
	
		public AgeException(String message) {
			super(message);
		}
	
	}

处理器

	@org.springframework.stereotype.Controller
	@RequestMapping("/test")
	public class MyController {
		
		@RequestMapping("/register.do")
		public ModelAndView doRegister(String name, int age) {
			
			int i = 3/0;
			
			ModelAndView mv = new ModelAndView();
			mv.addObject("name", name);
			mv.addObject("age", age);
			mv.setViewName("welcom.jsp");
			return mv;
		}
		
	}

error.jsp

	<body>
		error page.<br />
		${ex.message}
	</body>

nameError.jsp

	name error page.
	<br>
	${ex.message}
	
ageError.jsp

	age error page.
	<br>
	${ex.message}

##### 4.2.2 自定义异常处理器

使用springmvc定义好的SimpleMappingExceptionResolver异常处理器，可以实现生指定异常后的跳转。但若要想实现在捕捉到异常时，进行一些操作，它是完成不了的。此时，就需要使用自定义异常。

自定义异常处理器，需要实现HandlerExceptionResolver接口，并且该类需要在springmvc配置文件中进行注册。

自定义异常处理器

	public class MyHandlerExceptionResolver implements HandlerExceptionResolver {
	
		@Override
		public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler,
				Exception ex) {
			ModelAndView mv = new ModelAndView();
			mv.addObject("ex", ex);
			
			mv.setViewName("/errors/error.jsp");
			
			if(ex instanceof NameException) {
				mv.setViewName("/errors/nameError.jsp");
			}
	
			if(ex instanceof AgeException) {
				mv.setViewName("/errors/ageError.jsp");
			}
			
			return mv;
		}
	
	}

springmvc.xml 注册

	<!-- 注册自定义异常处理器 -->
	<bean class="com.bjpowernode.resolvers.MyHandlerExceptionResolver"></bean>
	
##### 4.2.3 异常处理器注解@ExceptionHandler

定义异常处理器

	@org.springframework.stereotype.Controller
	public class BaseController {
			
		@ExceptionHandler(NameException.class)
		public ModelAndView handlerNameException(Exception ex) {
			ModelAndView mv = new ModelAndView();
			mv.addObject("ex", ex);
			mv.setViewName("/errors/nameError.jsp");
			return mv;
		}
	
		@ExceptionHandler(AgeException.class)
		public ModelAndView handlerAgeException(Exception ex) {
			ModelAndView mv = new ModelAndView();
			mv.addObject("ex", ex);
			mv.setViewName("/errors/ageError.jsp");
			return mv;
		}
	
		@ExceptionHandler
		public ModelAndView handlerException(Exception ex) {
			ModelAndView mv = new ModelAndView();
			mv.addObject("ex", ex);
			mv.setViewName("/errors/error.jsp");
			return mv;
		}
		
	}

处理器继承异常处理器


	@org.springframework.stereotype.Controller
	@RequestMapping("/test")
	public class MyController extends BaseController {
		
		@RequestMapping("/register.do")
		public ModelAndView doRegister(String name, int age) throws StudentException {
			
			if(!"beijing".equals(name)) {
				throw new NameException("用户名不正确");
			}
			
			if(age > 60) {
				throw new AgeException("年龄太大了");
			}
			
			ModelAndView mv = new ModelAndView();
			mv.addObject("name", name);
			mv.addObject("age", age);
			mv.setViewName("/welcome.jsp");
			return mv;
		}
		
	}
	
#### 4.3 类型转换器和初始化参数绑定

可以解决提交的参数类型转换问题

#### 4.4 数据验证

springmvc支持JSR（Java Specification Requests）303-bean validation数据验证规范。而该规范的实现者很多，其中较常用的是Hibernate Valicator。

需要注意的是，Hebernate Valicator是与Hibernate ORM并列的Hibernate的产品之一。


第一步：导入jar包

Hibernate Valicator的jar包：

*	hibernate-validator

*	jboss-logging

*	validation-api

第二步：注册验证器和驱动

springmvc.xml

	<!-- 注册验证器 -->
	<bean id="myValidator" class="org.springframework.validation.beanvalidation.LocalValidatorFactoryBean">
		<property name="providerClass" value="org.hibernate.validator.HibernateValidator"></property>
	</bean>
	
	<!-- 注册mvc注解驱动 -->
	<mvc:annotation-driven validator="myValidator"></mvc:annotation-driven>
	
第三步：实体类-验证规则

	public class Student {
		@NotNull(message="姓名不以为空")
		@Size(min=3, max=6, message="姓名长度在{min}-{max}个字符之间")
		private String name;
		
		@Min(value=0, message="成绩不能小于{value}")
		@Max(value=100, message="成绩不能大于{value}")
		private double score;
		
		@NotNull(message="电话不能为空")
		@Pattern(regexp="^1[34578]\\d{9}$",message="手机号格式不正确")
		private String mobile;
		
		....
		getter and setter
		...
		
	}
	
第四步：处理器

	@org.springframework.stereotype.Controller
	@RequestMapping("/test")
	public class MyController {
		@RequestMapping(value={"/register.do"}, method= RequestMethod.POST)
		public ModelAndView doRegister(@Validated Student student, BindingResult br) {
			
			ModelAndView mv = new ModelAndView();
			mv.addObject("name", student);
			mv.setViewName("/WEB-INF/jsp/welcome.jsp");
			
			int errorCount = br.getErrorCount();
			if(errorCount > 0) {
				FieldError nameError = br.getFieldError("name");
				FieldError scoreError = br.getFieldError("score");
				FieldError mobieError = br.getFieldError("mobile");
				
				if(nameError != null) {
					String nameErrorMSG = nameError.getDefaultMessage();
					mv.addObject("nameErrorMSG", nameErrorMSG);
				}
				
				if(scoreError != null) {
					String scoreErrorMSG = scoreError.getDefaultMessage();
					mv.addObject("scoreErrorMSG", scoreErrorMSG);
				}
	
				if(mobieError != null) {
					String mobileErrorMSG = mobieError.getDefaultMessage();
					mv.addObject("mobileErrorMSG", mobileErrorMSG);
				}
				mv.setViewName("/index.jsp");
			}
			
			
			return mv;
		}
	}

index.jsp

	<form action="${pageContext.request.contextPath }/test/register.do" method="POST">
		姓名：<input type="text" name="name" />${nameErrorMSG }<br />
		成绩：<input type="text" name="score" />${scoreErrorMSG }<br />
		电话：<input type="text" name="mobile" />${mobileErrorMSG }<br />
		
		<input type="submit" value="注册" />
	</form>

#### 4.5 文件上传

##### 4.5.1 上传单个文件

第一步：导入jar包

*	commons-fileupload

*	commons-io

第二步：注册

	<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
		<property name="defaultEncoding" value="utf-8"></property>
		<property name="maxUploadSize" value="1048576"></property>
	</bean>
	
	<mvc:annotation-driven></mvc:annotation-driven>

第三步：页面

	<form action="${pageContext.request.contextPath }/test/upload.do" method="POST" enctype="multipart/form-data">
		文件：<input type="file" name="img" /><br />
		<input type="submit" value="提交" />
	</form>

第四步：处理器

	@org.springframework.stereotype.Controller
	@RequestMapping("/test")
	public class MyController {
		@RequestMapping(value={"/upload.do"}, method= RequestMethod.POST)
		public String doFileUpload(MultipartFile img, HttpSession session) throws Exception {
			String path = session.getServletContext().getRealPath("/images");
			
			if(img.getSize() > 0) {			
				String fileName = img.getOriginalFilename();
				if(fileName.endsWith("jpg") || fileName.endsWith("png")) {				
					File file = new File(path, fileName);
					img.transferTo(file);
					return "/success.jsp";
				}
			}
			return "/fail.jsp";		
		}
		
	}

##### 4.5.2 上传多个文件

处理器

	@org.springframework.stereotype.Controller
	@RequestMapping("/test")
	public class MyController {
		@RequestMapping(value={"/upload.do"}, method= RequestMethod.POST)
		public String doFileUpload(@RequestParam MultipartFile[] imgs, HttpSession session) throws Exception {
			String path = session.getServletContext().getRealPath("/images");
			
			for(MultipartFile img:imgs) {		
				if(img.getSize() > 0) {			
					String fileName = img.getOriginalFilename();
					if(fileName.endsWith("jpg") || fileName.endsWith("png")) {				
						File file = new File(path, fileName);
						img.transferTo(file);
					}
				}
			}
			
			return "/success.jsp";		
		}
		
	}

index.jsp

	<form action="${pageContext.request.contextPath }/test/upload.do" method="POST" enctype="multipart/form-data">
		文件1：<input type="file" name="imgs" /><br />
		文件2：<input type="file" name="imgs" /><br />
		文件3：<input type="file" name="imgs" /><br />
		<input type="submit" value="提交" />
	</form>
	
#### 4.6 拦截器

springmvc中的拦截器是非常重要和相当有用的，它的主要作用是拦截指定的用户请求，并进行相应的预处理与后处理。其拦截的时间点在“处理器映射器根据用户提交的请求映射出了所要执行的处理器类，并且也找到了要执行该处理器类的处理器适配器，在处理器适配器执行处理器之前”。当然，在处理器映射器映射出所要执行的处理器类时，已经将拦截器与处理器组合为了一个处理器执行链，并返回给了中央调度器。

##### 4.6.1 拦截器的实现

第一步：注册拦截器

	<!-- 注册拦截器 -->
	<mvc:interceptors>
		<mvc:interceptor>
			<mvc:mapping path="/**"/>
			<bean class="com.bjpowernode.interceptors.OneInterceptor"></bean>
		</mvc:interceptor>
	</mvc:interceptors>
	
第二步：拦截器

	public class OneInterceptor implements HandlerInterceptor {	
		@Override
		public boolean preHandle(HttpServletRequest arg0, HttpServletResponse arg1, Object arg2) throws Exception {
			System.out.println("execute prehandler");
			return true;
		}	
	
		@Override
		public void postHandle(HttpServletRequest arg0, HttpServletResponse arg1, Object arg2, ModelAndView arg3)
				throws Exception {
		
			System.out.println("execute posthandler");
		}
	
		@Override
		public void afterCompletion(HttpServletRequest arg0, HttpServletResponse arg1, Object arg2, Exception arg3)
				throws Exception {
			System.out.println("execute aftercomplete");
		}
	}

处理器

	@org.springframework.stereotype.Controller
	@RequestMapping("/test")
	public class MyController {
		@RequestMapping("/some.do")
		public String doSome() {
			System.out.println("execute dosome");
			return "/WEB-INF/jsp/welcome.jsp";
		}
		
	}

##### 4.6.2 权限拦截器

处理器

	@org.springframework.stereotype.Controller
	@RequestMapping("/test")
	public class MyController {
		@RequestMapping("/some.do")
		public String doSome() {
			System.out.println("execute dosome");
			return "/WEB-INF/jsp/welcome.jsp";
		}
		
	}

拦截器

	public class PermissionInterceptor implements HandlerInterceptor {
	
		@Override
		public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
				throws Exception {
			
			String user = (String) request.getSession().getAttribute("user");
			if("beijing".equals(user)) {
				return true;
			}
			request.getRequestDispatcher("/fail.jsp").forward(request, response);
			return false;
		}
	
		@Override
		public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
				ModelAndView modelAndView) throws Exception {
		}
	
		@Override
		public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
				throws Exception {
		}
	
	}

注册

	<!-- 注册拦截器 -->
	<mvc:interceptors>
		<mvc:interceptor>
			<mvc:mapping path="/**"/>
			<bean class="com.bjpowernode.interceptors.PermissionInterceptor"></bean>
		</mvc:interceptor>
	</mvc:interceptors>

login.jsp

	<%
		session.setAttribute("user", "beijing");
	%>>
	login success.

logout.jsp

	<%
		session.removeAttribute("user");	
	%>
	logout success.
