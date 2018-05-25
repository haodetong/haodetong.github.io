---
title: 第二章 springmvc配置式开发
date: 2018-05-22 16:49:31
tags: springmvc
category: springmvc

---

#### 2.1 处理器映射器 HandlerMapping

HandlerMapping接口负责根据request请求找到相应的Handler处理器及Interceptor拦截器，并将它们封装在HandlerExecutionCahin对象中，返回给中央调度器。

其常用的两种实现类：

*	BeanNameUrlHandlerMapping

*	SimpleUrlHandlerMapping

##### (1)、BeanNameUrlHandlerMapping

BeanNameUrlHandlerMapping处理器映射器，会根据请求的url与spring容器中定义的处理器的bean的name值进行匹配，从而在spring容器中找到处理器bean的实例。

	<bean id="/my.do" class="com.bjpowernode.handlers.MyController"></bean>

##### (2)、SimpleUrlHandlerMapping

SimpleUrlHandlerMapping处理器映射器，不仅可以将url与处理器分离，还可以对url进行统一映射管理。

SimpleUrlHandlerMapping处理器映射器，会根据请求的url与spring容器中定义的处理器映射器子标签的key属性进行匹配。匹配上后，再将该key的value值与处理器的bean的id值进行匹配，从而在spring容器中找到处理器bean。

	<!-- 注册处理器 -->
	<bean id="myController" class="com.bjpowernode.handlers.MyController"></bean>
	<!-- 注册处理器映射器: SimpleUrlHandlerMapping-->
	<bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
		<!-- 
		<property name="mappings">
			<props>
				<prop key="/hello.do">myController</prop>
				<prop key="/world.do">myController</prop>
			</props>
		</property>
		 -->
		 <property name="urlMap">
		 	<map>
		 		<entry key="/hello.do" value="myController"></entry>
		 		<entry key="/world.do" value="myController"></entry>
		 	</map>
		 </property>
	</bean>
	
#### 2.2 处理器适配器 HandlerAdapter

前面所写的代码中，之所以要将Handler定义为Controller接口的实现类，就是因为这里使用的处理器适配器是SimpleControllerAdapter。打开其源码，可以看到将handler强转为了Controller。在定义Handler时，若不将其定义为Controller接口的实现类，这里的强转会出错。

当然，中央调度器首先会调用该适配器的supports()方法，判断该Handler是否与Controller具有is-a的关系。在具有is-a的关系的前提下，才会强转。

常用的两种处理器适配器：

（1）、SimpleControllerHandlerAdapter


代码：

	public class MyController implements Controller {
		
			@Override
			public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
				ModelAndView mv = new ModelAndView();
				//其底层执行的是request.setAttribute()方法
				mv.addObject("message", "hello world");
				mv.setViewName("welcome");
				return mv;
			}
		
		}

（2）、HttpRequstHandlerAdapter

代码:

	public class MyController implements HttpRequestHandler{

		@Override
		public void handleRequest(HttpServletRequest request, HttpServletResponse response)
				throws ServletException, IOException {
			request.setAttribute("message", "hello sprngmvcworld");
			request.getRequestDispatcher("/WEB-INF/jsp/welcome.jsp").forward(request, response);
		}
	
	}

#### 2.3 处理器

处理器除了实现Controller接口外，还可以继承自一些其它的类来完成一些特殊的功能。

（1）、继承自AbstractController类

若处理器继承自AbstractController类，那么该控制器就具有了一些新的功能。因为AbstractController类还继承自一个父类WebContentGenerator。

WebContentGenerator类具有supportedMethods属性，可以设置支持的Http数据提交方式。默认支持GET,POST。

	public class MyController extends AbstractController{
	
		@Override
		protected ModelAndView handleRequestInternal(HttpServletRequest request, HttpServletResponse response)
				throws Exception {
			ModelAndView mv = new ModelAndView();
			//其底层执行的是request.setAttribute()方法
			mv.addObject("message", "hello world");
			mv.setViewName("welcome");
			return mv;
		}
		
	}

springmvc.xml

	<bean id="/my.do" class="com.bjpowernode.handlers.MyController">
		<property name="supportedMethods" value="POST"></property>
	</bean>

（2）、继承自MultiActionController类

springmvc.xml

	<!-- 注册处理器 -->
	<bean id="myController" class="com.bjpowernode.handlers.MyController"></bean>
	<!-- 注册处理器映射器: SimpleUrlHandlerMapping-->
	<bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
		 <property name="urlMap">
		 	<map>
		 		<!-- 
					MultiActionController类具有一个属性：方法名解析器methodNameResolver，
					其具有默认值InternalPathMethodNameResolver，
					该解析器将控制器中的方法名作为url中的资源名称进行解析，
					那了就意味着，我们提交请求时，要将方法名作为资源名称出现。
				 -->
		 		<entry key="/my/*.do" value="myController"></entry>
		 	</map>
		 </property>
	</bean>

控制器

	public class MyController extends MultiActionController{
	
		public ModelAndView doFirst(HttpServletRequest request, HttpServletResponse response)
				throws Exception {
			ModelAndView mv = new ModelAndView();
			mv.addObject("message", "execute dofisrt method");
			mv.setViewName("/WEB-INF/jsp/welcome.jsp");
			return mv;
		}
	
		public ModelAndView doSecond(HttpServletRequest request, HttpServletResponse response)
				throws Exception {
			ModelAndView mv = new ModelAndView();
			mv.addObject("message", "execute dosecond method");
			mv.setViewName("/WEB-INF/jsp/welcome.jsp");
			return mv;
		}
		
	}

访问路径	

	http://localhost:8080/03-MultiActionController-test/my/doFirst.do
	
	http://localhost:8080/03-MultiActionController-test/my/doSecond.do

方法名解析器methodNameResolver，除了具有默认值InternalPathMethodNameResolver方法名解析器以外，还具有另外两个方法名解析器：PropertiesMethodNmaeResolver，ParameterMethodNameResolver。


#### 2.4 视图解析器

ModelAndView 即模式与视图，通过addObject()方法向模型中添加数据，通过setViewName()方法向模型添加视图名称。

跟踪addObject()方法，可以知道这里的模型就是ModelMap，而ModelMap的本质就是个HashMap，向模型中添加数据，就是向ModelMap中添加数据。

##### （1）、内部资源视图解析器InternalResourceViewResolver

该视图解析器用于完成当前应用内部资源的封装与跳转。

	<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/jsp/"></property>
		<property name="suffix" value=".jsp"></property>
	</bean>

##### （2）、BeanNameViewResolver视图解析器

该视图解析器用于将资源封装为spring容器中注册的bean实例。

*	RedirectView：定义外部资源视图对象

*	JstlView：定义内部资源视图对象

springmvc.xml

	<!-- 注册视图解析器 -->
	<bean class="org.springframework.web.servlet.view.BeanNameViewResolver"></bean>
	
	<!-- 注册内部和外部视图bean -->
	<bean id="internalResource" class="org.springframework.web.servlet.view.JstlView">
		<property name="url" value="WEB-INF/jsp/welcome.jsp"></property>
	</bean>
	<bean id="taobao" class="org.springframework.web.servlet.view.RedirectView">
		<property name="url" value="http://www.taobao.com"></property>
	</bean>

控制器

	public class MyController implements Controller {
	
		@Override
		public ModelAndView handleRequest(HttpServletRequest arg0, HttpServletResponse arg1) throws Exception {
			return new ModelAndView("taobao");
		}
		
	}

##### （3）、XmlViewResolver视图解析器

专门用来定义视图bean的配置文件myViews.xml

	<bean id="internalResource" class="org.springframework.web.servlet.view.JstlView">
		<property name="url" value="WEB-INF/jsp/welcome.jsp"></property>
	</bean>
	<bean id="taobao" class="org.springframework.web.servlet.view.RedirectView">
		<property name="url" value="http://www.taobao.com"></property>
	</bean>

springmvc.xml

	
	<!-- 注册XmlViewResolver视图解析器 -->
	<bean class="org.springframework.web.servlet.view.XmlViewResolver">
		<property name="location" value="classpath:myViews.xml"></property>
	</bean>
	

##### （4）、ResourceBundleViewResolver视图解析器

myVews.properties

	taobao.(class)=org.springframework.web.servlet.view.RedirectView
	taobao.url=http://www.taobao.com
	
	internalResource.(class)=org.springframework.web.servlet.view.JstlView
	internalResource.url=/WEB-INF/jsp/welcome.jsp

springmvc.xml

	<!-- 注册处理器 -->
	<bean id="/my.do" class="com.bjpowernode.handlers.MyController"></bean>
	
	<!-- 注册视图解析器 -->
	<bean class="org.springframework.web.servlet.view.ResourceBundleViewResolver">
		<property name="basename" value="myViews"></property>
	</bean>


注：视图解析器的优先级

有时经常需要应用一些视图解析器策略来解析视图名称，即当同时存在多个视图解析器均可解析ModelAndView中的同一视图名称时，哪个视图解析器会起作用呢？

视图解析器有一个order属性，专门用于设置多个视图解析器的优先级。数字越小，优先级越高。一般不会InternalResourceViewResolver解析器指定优先级，即让其优先级最低。
















