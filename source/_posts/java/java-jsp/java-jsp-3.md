---
title: 第3章 jsp系统开发模型
date: 2018-03-07 11:23:34
tags: jsp
category: java
keywords: java, jsp
description: java jsp
---

### 第3章 系统开发模型

#### 3.1.1 纯jsp

#### 3.1.2 jsp+javaBean的Model1

广义的javaBean分为数据承载Bean和业务处理Bean

狭义的javaBean，需要满足javaBean规范。

* public 修饰
* 实现Serializable接口
* 无参构造器
* 成员变量私有化，且提供getter和setter

#### 3.1.3 MVC的Modle2

#### 3.1.4 三层架构

View层：视图层、表现层、Web层。

Service层：业务层、逻辑层。

Dao层：持久层、数据访问层。data access object

三层架构的设计中，采用面向抽象编程。即上层对下层的调用，是通过调用接口的方法实现的。而下层对上层真正的服务提供者，是下层接口的实现类。

提供报务的接口相同，服务的实现类可以更换，实现了层间解耦。

#### 3.1.5 MVC+三层架构	

View Level：Contoller(Servlet) + View(jsp)

Service Level：Interfaces + Impls

Dao Level：Interfaces + Impls

将MVC的Model分为了两层：Service层与Dao层，分别用于处理业务逻辑和持久化操作。

### 3.2 学生注册登录系统

package: com.test.beans

//定义狭义的javaBean Student

	public class Sdudent implements Serializable {
		
		private static final long serialVersionUID = 1L;
		private Integer id;//业务无关主键
		private String password;
		private String num;//学号
		private String name;
		private String age;
		private double score;
		public Sdudent() {
			super();
		}
		public Sdudent(String num, String name, String age, double score) {
			super();
			this.num = num;
			this.name = name;
			this.age = age;
			this.score = score;
		}
		public Integer getId() {
			return id;
		}
		public void setId(Integer id) {
			this.id = id;
		}
		public String getPassword() {
			return password;
		}
		public void setPassword(String password) {
			this.password = password;
		}
		public String getNum() {
			return num;
		}
		public void setNum(String num) {
			this.num = num;
		}
		public String getName() {
			return name;
		}
		public void setName(String name) {
			this.name = name;
		}
		public String getAge() {
			return age;
		}
		public void setAge(String age) {
			this.age = age;
		}
		public double getScore() {
			return score;
		}
		public void setScore(double score) {
			this.score = score;
		}
		@Override
		public String toString() {
			return "Sdudent [id=" + id + ", password=" + password + ", num=" + num + ", name=" + name + ", age=" + age
					+ ", score=" + score + "]";
		}
		
	}

建表sdudent

//login.jsp 修改欢迎页面为login.jsp

	<form action="${pageContext.request.contextPath }/loginServlet">
		学号：<input type="text" name="num" />
		密码：<input type="text" name="password" />
		<input type="submit" value="登录" />
	</form>
	<a href="${pageContext.request.contextPath}/register.jsp">注册</a>

//register.jsp

	${message }<br />
	<form action="${pageContext.request.contextPath }/registerServlet">
		学号：<input type="text" name="num" />
		密码：<input type="text" name="password" />
		<input type="submit" value="登录" />
	</form>

//连接数据库，工具类

	package com.test.utils;
	
	import java.sql.Connection;
	import java.sql.DriverManager;
	import java.sql.ResultSet;
	import java.sql.SQLException;
	import java.sql.Statement;
	
	public class JdbcUtils {
		//加载DB驱动
		static {
			//将驱动mysql-connector-java-5.1.7-bin.jar放到lib下
			try {
				Class.forName("com.mysql.jdbc.Driver");
			} catch (ClassNotFoundException e) {
				e.printStackTrace();
			}
		}
		
		private static Connection conn;		
		
		//获取connection对象
		public static Connection getConnection() throws SQLException {
			
			String url = "jdbc:mysql://127.0.0.1:3306/test";
			String user = "root";
			String password = "";
			if(conn == null || conn.isClosed()) {
				conn = DriverManager.getConnection(url, user, password);
			}
			return conn;
			
		}
		
		//关闭资源
		public static void close(Connection conn, Statement stmt, ResultSet rs) throws SQLException {
			
			if(conn != null && !conn.isClosed()) {
				conn.close();
			}
			if(stmt != null && !stmt.isClosed()) {
				stmt.close();
			}
			if(rs != null && !rs.isClosed()) {
				rs.close();
			}
			
		}
		
	}
	
//登录的servlet

	package com.test.servlets;
	
	public class LoginServlet extends HttpServlet {
		private static final long serialVersionUID = 1L;
		protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			//1、接收请求参数
			String num = request.getParameter("num");
			String password = request.getParameter("password");
			
			HttpSession session = request.getSession();
			
			if(num == null || "".equals(num.trim())) {
				session.setAttribute("message", "输入不能为空");
				response.sendRedirect(request.getContextPath() + "/login.jsp");
				return;
			}
			
			//2、创建Service对象
			IStudentService service = new StudentServiceImpl();
			
			//3、调用Service对象的checkUser验证方法
			Student sdudent = service.checkUser(num, password);
			
			//4、验证失败，跳转到登录页面
			if(sdudent == null) {
				session.setAttribute("message", "输入错误");
				response.sendRedirect(request.getContextPath() + "/login.jsp");
				return;
			}
			
			//5、验证通过，跳转到index.jsp
			response.sendRedirect(request.getContextPath() + "/index.jsp");
			
		}
	}

//注册的servlet
	
	package com.test.servlets;
	public class RegisterServlet extends HttpServlet {
		private static final long serialVersionUID = 1L;
	       
		protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			
			//1、获取表单参数
			request.setCharacterEncoding("UTF-8");
			String num = request.getParameter("num");
			String password = request.getParameter("password");
			String name = request.getParameter("name");
			String ageStr = request.getParameter("age");
			String scoreStr = request.getParameter("score");
			
			Integer age = Integer.valueOf(ageStr);
			Double score = Double.valueOf(scoreStr);
			
			//2、创建Student对象
			Student student = new Student(num,name,age,score);
			student.setPassword(password);
			
			//3、创建Service对象
			IStudentService service = new StudentServiceImpl();
			
			//4、调用Service对象的saveStudent()方法，写入DB
			Integer id = service.saveStudent(student);
			
			//5、写入失败，跳转到注册页面，重新注册
			if(id == null) {
				response.sendRedirect(request.getContextPath() + "/register.jsp");
			}
			
			//6、写入成功，跳转到登录页面
			response.sendRedirect(request.getContextPath() + "/login.jsp");
		}
	
	}

service层

//服务层接口

	package com.test.service;
	
	import com.test.beans.Student;
	
	public interface IStudentService {
		//对登录用户进行验证
		Student checkUser(String num, String password);
		
		//向DB中添加Student
		Integer saveStudent(Student student);
	}

//服务层实现类
	
	package com.test.service;

	import com.test.beans.Student;
	import com.test.dao.IStudentDao;
	import com.test.dao.SdudentDaoImpl;
	
	public class StudentServiceImpl implements IStudentService {
		private IStudentDao dao;
		
		public StudentServiceImpl() {
			dao = new SdudentDaoImpl();
		}
	
		@Override
		public Student checkUser(String num, String password) {
			return dao.selectStudentLogin(num,password);
		}
	
		@Override
		public Integer saveStudent(Student student) {
			return dao.insertStudent(student);
		}
		
	}

//dao层

//dao层接口

	package com.test.dao;
	
	import com.test.beans.Student;
	
	public interface IStudentDao {
	
		Student selectStudentLogin(String num, String password);
	
		Integer insertStudent(Student student);
	
	}

//dao层实现类

	package com.test.dao;
	
	import java.sql.Connection;
	import java.sql.PreparedStatement;
	import java.sql.ResultSet;
	import java.sql.SQLException;
	import java.sql.Statement;
	
	import com.test.beans.Student;
	import com.test.utils.JdbcUtils;
	
	public class SdudentDaoImpl implements IStudentDao {
		private Connection conn;
		private Statement stmt;
		private PreparedStatement ps;
		private ResultSet rs;
		
		@Override
		public Student selectStudentLogin(String num, String password) {
			Student student = null;
			try {
				conn = JdbcUtils.getConnection();
				String sql = "select * from student where num=? and password=?";
				ps = conn.prepareStatement(sql);
				ps.setString(1, num);
				ps.setString(2, password);
				rs = ps.executeQuery();
				if(rs.next()) {
					student = new Student();
					student.setId(rs.getInt("id"));
					student.setNum(rs.getString("num"));
					student.setPassword(rs.getString("password"));
					student.setName(rs.getString("name"));
					student.setAge(rs.getInt("age"));
					student.setScore(rs.getDouble("score"));
				}
			} catch (SQLException e) {
				e.printStackTrace();
			} finally {
				try {
					JdbcUtils.close(conn, ps, rs);
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
			return student;
		}
	
		@Override
		public Integer insertStudent(Student student) {
			Integer id = null;
			try {
				conn = JdbcUtils.getConnection();
				String sql = "insert into student(num,password,name,age,score) values(?,?,?,?,?)";
				ps = conn.prepareStatement(sql);
				ps.setString(1, student.getNum());
				ps.setString(2, student.getPassword());
				ps.setString(3, student.getName());
				ps.setInt(4, student.getAge());
				ps.setDouble(5, student.getScore());
				
				ps.executeUpdate();
				
				sql = "select @@identity newId";
				ps = conn.prepareStatement(sql);
				rs = ps.executeQuery();
				if(rs.next()) {
					id = rs.getInt("newId");
				}
				
			} catch (SQLException e) {
				e.printStackTrace();
			} finally {
				try {
					JdbcUtils.close(conn, ps, rs);
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
			return id;
		}
	
	}
