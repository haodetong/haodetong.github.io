---
title: Restangular:专门用来与外界通信的angularjs库
date: 2016-05-13 15:14:59
tags: angular
category: angularjs
keywords: angular, angularjs
description: Restangular:专门用来与外界通信的angularjs库

---

####一、安装restangular

restangular依赖Lo-Dash或Underscore，需要引入这两个库中的任意一个。

	<script type="text/javascript" src="restangular.js"></script>
	<script type="text/javascript" src="lodash.js"></script>
	
通过Bower安装Restangular，Lo-Dash会同时被自动下载。

	$ bower install restangular
	
将restangular当作依赖加载进应用模块。

	var app = angular.module('app', ['restangular']);
	
完成后，就可以把Restangular服务注入到angularjs对象中。

	app.factory('UserService', ['Restangular', function(Restangular) {
	    //现在我们已经在UserService中访问了Restangular 
	}])
	
####二、Restangular对象简介

Restangular有两种方式创建拉取数据的对象：

1、.all方法

	var User = Restangular.all('users');
	var allUsers = User.getList(); 
	
这样设置Restangular服务会让所有的HTTP请求将/users路径作为根路径来拉取数据，getList()方法会从 /users 拉取数据。

2、.one方法

	var oneUser = Restangular.one('users', 'abc123');
	oneUser.get().then(function(user) {
	     user.getList('inboxes');
	});
	
调用oneUser上的get()时向 /users/abc123/inboxes 发送请求。

####三、使用Restangular

当Restangular将初始化的对象返回给我们后，可以通过几种不同的方法与后端API进行交互。

1、getList() 获取信息

	var message = Restangular.all('message');
	var allMessages = message.getList();
	
2、post() 创建信息

	var message = Restangular.all('message');
	var newMessage = {
	    body:'Hello World'
	}
	message.post(newMessage);
	
3、Restangular返回的是增强过的promise,因此除了可以调用then方法,还可以调用一些特殊的方法,比如$object。$object会立即返回一个空数组（或对象），在服务器返回信息后，数组会被新数据填充。 这对更新一个集合后，在作用域中立即重新获取集合时很有用：

	message.post(newMessage).then(function(newMsg){
	    $scope.message = message.getList().$object;
	}, function(errorReason){
	    //错误
	})
	
4、remove() 发送一个delete http请求给后端

	var message = message.get(123);
	message.remove();

5、put() 支持对象的更新和存储

*  要更新一个对象，首先要查询这个对象，然后在实例中设置新属性值，再调用对象的put()方法将更新保存到后端。

*  在更新一个对象前对其进行复制，然后对复制的对象进行修改和保存。Restangular.copy()支持复制功能。

6、嵌套资源查询

	var author = Restangular.one('authors','abc123');    //构建一个GET到/authors/abc123/books的请求
	var books = author.getList('books');    

7、自定义函数 customMETHORD()

METHORD可以被以下方法替换：GET,GETLIST,DELETE,POST,PUT,HEAD,OPTIONS,PATCH,TRACE.

举例：

	author.customGET('aaa');

或

	auto.customPOST(
	    {body: 'bbbbb'}, //post body
	    'aaa', //路由
	    {}, //自定义参数
	    {}, //自定义头部
	)    

####四、设置Restangular

1、设置baseUrl

给所有后端API请求设置baseUrl:

	angular.module('myApp', ['restangular'])
	     .config(function(RestangularProvider) {
	         RestangularProvider.setBaseUrl('/api/v1');
	     });

2、添加元素转换

elementTransformers 这个方法会在资源被加载后当做回调函数使用，在AngularJS对象中使用这些资源前，可以对其进行更新或修改。

	angular.module('myApp', ['restangular'])
	    .config(function(RestangularProvider) {    
	         //3个参数：第一个路由、第二个不详、第三个回调
	        RestangularProvider.addElementTransformer('authors', false, function(element) {
	            element.fetchedAt = new Date();
	            return element;
	        });
	});

扩展数据模型或集合

	angular.module(‘myApp’, [‘restangular’])
	
	.config(function(RestangularProvider) {   
	     RestangularProvider.extendModel('authors', function(element) {
	         element.getFullName = function() {
	             return element.name + ' ' + element.lastName;
	         };
	         return element;
	     });
	});

3、响应拦截器 responseInterceptors

即为在响应从服务器返回时调用，调用时传入以下参数：

*  data:从服务器返回的数据
*  operation:使用的HTTP方法
*  what:所请求的数据模型
*  url:请求的相对URL
*  response:完整的服务器响应，包括响应头
*  deferred:请求的promise对象

下面的设置会使getList()返回一个带有元信息的数据。例如：向 /customers 发送get请求会返回一个像{customers: []}这样的数据

	angular.module('myApp', ['restangular'])
	.config(function(RestangularProvider) {
	    RestangularProvider.setResponseInterceptor(function(data, operation, what) { 
	        if (operation == 'getList') {
	            var list = data[what];
	            list.metadata = data.metadata;
	            return list;
	        }
	        return data;
	    });
	});

4、请求拦截器 requestInterceptors

即为在向服务器发送请求之前进行操作。

tips:响应拦截器和请求拦截器应用小实例：我们可以同时使用他们来实现全页面范围内的加载提示。在每个请求之前开始加载提示，在收到请求后停止加载提示

传入以下参数：

*  element:发送给服务器的资源
*  operation:使用的HTTP方法
*  what:所请求的数据模型
*  url:请求的相对URL

```
angular.module('myApp', ['restangular'])
.config(function(RestangularProvider) {
       RestangularProvider.setRequestInterceptor(function(elem, operation, what) { 
           if (operation === 'put') {
            elem._id = undefined;
            return elem;
        }
        return elem;
    });
   });
```

5、errorInterceptors 捕获错误

如果errorInterceptor返回false，promise链就会被中断，并且我们永远不需要处理错误。

例如，此时是处理验证失败的好时机，任何请求如果返回了401，可以通过errorInterceptor将其捕获并将用户重定向到登录页面。

	angular.module('myApp', ['restangular'])
	    .config(function(RestangularProvider) {
	        RestangularProvider.setErrorInterceptor(function(resp) { 
	            displayError();
	            return false; //停止promise链  
	        });
	    });

五、建议将restangular封装在一个自定义的服务对象内。通过将restangular服务注入到自定义服务中，就可以方便的对restangular进行封装。在函数内部，使用withConfig()函数来创建自定义设置。

	angular.module('myApp', ['restangular']).factory('MessageService', ['Restangular', function(Restangular) {
	    var restAngular = Restangular.withConfig(function(Configurer) { 
	            Configurer.setBaseUrl('/api/v2/messages');
	    });
	    var _messageService = restAngular.all('messages');
	    return {
	        getMessages: function() {
	            return _messageService.getList();
	        }
	    }; 
	}]);