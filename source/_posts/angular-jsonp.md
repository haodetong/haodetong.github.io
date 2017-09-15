---
title: angularJS使用.jsonp方法
date: 2016-05-16 15:39:59
tags: angular
category: angularjs
keywords: angular, jsonp
description: angularJS使用.jsonp方法

---

angularJS与服务器通信中的$http中有一个$http.jsonp方法，在网上偶然间看到一篇文章，对此解释得非常清楚。

原文地址：[【AngularJs】 – JSONP跨域访问数据传输](JSONP跨域访问数据传输)

json:数据存储格式.

jsonp:一种非官方的跨域数据交互协议.

AngularJS在http服务中的jsonp函数如下所示：

	$http.jsonp("https://api.github.com?callback=JSON_CALLBACK").success(function(data) {
	    //执行回调
	})

1、当请求被发送时，AngularJS会在DOM中生成一个如下所示的`<script>`标签：

	<script src="https://api.github.com?callback=angular.callbacks._0" type="text/javascript">

2、当支持JSONP的服务器返回数据时，数据会被包装在由AngularJS生成的具名函数angular.callbacks._0中。

3、在这个例子中，Github服务器响应如下：

	angular.callbacks._0({ 
	    'meta': {
	        'X-RateLimit-Limit': '60', 
	        'status': 200 
	    },
	    'data': {
	        'current_user_url': 'https://api.github.com/user' 
	    } 
	}) 

4、angularJS中jsonp的使用

	myUrl = "http://localhost:8090/api/test?callback=JSON_CALLBACK";
	
	$http.jsonp(myUrl).success(
	　　function(data){
	　　　　alert(data);
	　　}
	);

1.angularJS中使用$http.jsonp函数

2.指定callback和回调函数名，函数名为JSON_CALLBACK时，会调用success回调函数，JSON_CALLBACK必须全为大写。

3.也可以指定其它回调函数，但必须是定义在window下的全局函数。

4.url中必须加上callback

5.当callback为JSON_CALLBACK时，只会调用success，即使window中有JSON_CALLBACK函数，也不会调用该函数。