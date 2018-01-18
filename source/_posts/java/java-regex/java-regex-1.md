---
title: java-正则表达式
date: 2018-01-17 20:50:07
tags: regex
category: java
keywords: java, regex
description: java regex

---

正则表达式用于操作字符串数据。

#### 1、匹配：

	匹配手机号码
	String tel = "15022443344";
	String regex = "1[3578][0-9]{9}";
	String regex2 = "1[3578]\\d{9}";
	
	boolean r = tel.matches(regex);
	sosy(tel+":"+r);
	
	1[3578][0-9]{9}
	1[3578]\\d{9}
	
	1:第一位固定是1 
	[3578]:第二位是3578中的一个
	[0-9]:第三位是0-9中的一个
	{9}：第三位共出现9次
	\:对\进行转义
	\d:代表[0-9]
	
#### 2、切割：

其实使用的就是String类中的split(String regex)方法
	
	*:任意次数，0次，1次或多次
	?:0次或1次
	+:1次或多次
	
	String str = "adf djs    sfjjs";
	String names = str.split(" +");//空格出现一次或多次
	
	===========================
	
	.:代表任意字符
	String str = "adf.djss.fjjs";
	String names = str.split("\\.");
	
	():代表组
	//根据重复的t和重复的m切割三个字段
	String str = "zhangsantttttttxiaoqiangmmmmmmmmzhaoliu";
	String names = str.split("(.)\\1+");
	
	(.)\\1+
	.:代表第一个字符是任意字符，可以是t,也可以是m
	(.):默认代表的是第1组
	\\1:代表第二个字符是第1组的重复
	+:代表重复的组出现1次或多次
	
	组：((A)(B(C)))
	最外边的()代表第1组
	(A)是第2组
	(B(C))是第3组
	(C)是第4组
		
	
#### 3、替换：

	replaceAll(regex,replacement)
	
	//用#代替所有的连续的重复字符
 	String str = "zhangsantttttttxiaoqiangmmmmmmmmzhaoliu";
 	str = str.replaceAll("(.)\\1+", "#");
 	syso(str);
 	输出结果：
 	zhangsan#xiaoqiang#zhaoliu
	
	========================
	
	//将所有连续的重复字符变成单个,重复的t变成1个t，重复的m变成一个m
	String str = "zhangsantttttttxiaoqiangmmmmmmmmzhaoliu";
 	str = str.replaceAll("(.)\\1+", "$1");
	syso(str);
 	输出结果：
 	zhangsantxiaoqiangmzhaoliu
	
	$1:代表获取前一个参数中的组1
	
	============================
	
	String tel = "15022229999";
	tel = tel.replaceAll("(\\d{3})\\d{4}(\\d{4})","$1****$2");
	syso(tel);
	输出结果：
	150****9999
	
#### 4、获取

	//取三个字符组成的字符串
	public class RegexGetDemo {

		public static void main(String[] args) {
			
			String str = "hao jlalal jie lskqjx wxm jajlw";
			String regex = "\\b[a-z]{3}\\b";
			//将正则封装成对象
			Pattern p = Pattern.compile(regex);
			//获取匹配器对象
			Matcher m = p.matcher(str);
			//查找
			while(m.find()) {
				System.out.println(m.group());
			}	
		}
	}	
	输出结果：
	hao
	jie
	wxm
	
	\b:表示边界

#### 5、练习

	String str = "我我...我我我....我我我要...要要要.....学学学....编编....程程程";
	//去掉.
	str = str.replaceAll("\\.+", "");
	//去掉叠词
	str = str.replaceAll("(.)\\1+","$1");
	System.out.println(str);	
	
	输出结果：
	我要学编程
	
------------------

	//ip地址排序
	String ip_str = "192.168.9.30 127.0.0.1 4.4.4.5 101.80.8.44";
	
	System.out.println("str:"+ip_str);
	
	//补0，ip地址四段数字，不足3位的补足3位
	ip_str = ip_str.replaceAll("(\\d+)", "00$1");
	
	//四段数字全都保留32位
	ip_str = ip_str.replaceAll("0*(\\d{3})", "$1");
	
	//分割
	String[] ips = ip_str.split(" +");
	
	//treeSet排序
	TreeSet<String> ts = new TreeSet<String>();
	for(String ip:ips) {
		ts.add(ip);
	}
	
	//去掉补足3位的0
	System.out.println("排序");
	for(String ip:ts) {
		System.out.println(ip.replaceAll("0*(\\d+)","$1"));			
	}

--------

	//校验邮箱地址
	String mail = "test@126.com";//.cn .com.cn
	String regex = "[a-zA-Z0-9_]+@[a-zA-Z0-9]+(\\.[a-zA-Z]{1,3})+";
	boolean b = mail.matches(regex);
	System.out.println(b);
	
---------

网页爬虫，获取指定网页里所有的邮箱
	
	public static List<String> getMails() throws IOException {
		//源
		URL url = new URL("http://www.baidu.com/web/aaa.html");
		//读取源
		BufferedReader bufIn = new BufferedReader(new InputStreamReader(url.openStream()));
		//对源中的数据进行规则匹配
		String mail_regex = "\\w+@\\w+(\\.\\w)+";
		List<String> list = new ArrayList<String>();
		Pattern p = Pattern.compile(mail_regex);
		String line = null;
		while((line = bufIn.readLine()) != null) {
			Matcher m = p.matcher(line);
			while(m.find()) {
				//数据写入集合
				list.add(m.group());
			}		
		}
		bufIn.close();
		return list;
	}
