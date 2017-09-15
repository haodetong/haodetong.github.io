---
title: angularJS服务器通信之http
date: 2016-05-15 16:27:40
tags: angular
category: angularjs
keywords: angular, http
description: angularJS服务器通信之：$http 

---

搜索angularJS通信时，看到一篇对$http通信解释得很是详细的文章，更难得的是文章的最后有一个对$http的封装，正好用在自己的angularJS项目的架构中。

原来地址：[Angular服务器通信](http://www.cnblogs.com/wujie520303/p/4869600.html)

参考文献：[AngularJS权威教程](http://www.linuxidc.com/Linux/2015-01/111429.htm)

####一、快速上手

$http服务只接收一个配置对象作为参数，并且返回一个promise对象，两个回调方法success和error.

1、使用success()和error()

	$http({
	  method: 'GET',
	  url: '/api/user/1'
	}).success(function(rep) {
	  // ...
	}).error(function(err) {
	  // ...
	});

2、使用then()方法

	$http({
	  method: 'GET',
	  url: '/api/user/1'
	}).then(function(rep) {
	  // 成功
	}, function(err) {
	  // 失败
	});

3、服务器向客户端返回一个json数据，并带有一个状态码。

状态码约定为：

	var rep_status = [
	  {key: 'SUCCESS', value: 1, desc: '交互成功'},
	  {key: 'NOT_LOGIN', value: 2, desc: '未登录'},
	  {key: 'INVALID_REQUEST', value: 3, desc: '非法请求'},
	  {key: 'INVALID_PARAM', value: 4, desc: '参数错误'},
	  {key: 'INNER_ERROR', value: 5, desc: '服务器内部错误'},
	  {key: 'UNKNOWN', value: 6, desc: '未知错误'}
	]

返回API约定为：

	// 响应成功返回
	{
	  status: 1,
	  message: 'ok',
	  data: {
	    total: 200,
	    object_list: [
	      {
	        // ....
	      }
	    ]
	  }
	}
	
	// 出现错误后端返回
	{
	  status: 4,
	  message: '参数错误'
	}
	
4、通过success()和error()得到的响应数据只包含服务器响应数据的主体，如果想得到一个完整的对象，需要使用then()方法。一个完整的响应对象或者叫promise对象包含如下字段：

data - {string | Object} 响应主体 status - {number} 响应状态

headers - {function([headerName])} 获取响应头部的函数 

config - {Object} 请求触发时提供的配置对象，后面会详细介绍

statusText - {string} 响应状态描述*

	 var promise = $http({method: 'GET', url: '/api/user/5'});
	promise.then(function(rep) {
	  console.log(rep.data.data.name); // jenemy
	  console.log(rep.status); // 200
	  console.log(rep.statusText); // OK
	  console.log(rep.config.url); // /api/user/5
	  console.log(rep.headers()['x-powered-by']); // Express
	});

5、$http配置属性

// params参数会转化为?name=jenemy&age=25

	$http({
	  method: 'GET',
	  url: '/api/user/5',
	  params: {
	    name: 'jenemy',
	    age: 25
	  }
	});

// data - {string | object} 在发送POST或PUT时使用

	$http({
	    method: 'POST',
	    url: '/api/user/2',
	    data: {name: "jenemy"},
	    headers: {
	        'Authorization': 'Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==',
	        'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8'
	    }
	});

6、cache - {boolean | cache}

默认情况下，$http服务不会对请求进行本地缓存。启用缓存，需要向$http请求传入一个布尔值或者一个缓存实例。

	$http.get('/api/user', { cache: true})
	.success(function(data) {})
	.error(function(data){});

当第一次请求成功后，$http会缓存请求的url，下一次请求时会直接加载缓存中的数据，不会再产生一次$http请求发出。

由于设置了启用缓存，AngularJS默认会使用$cacheFactory，这个服务是AngularJS在启动时自动创建的。

如果想要对AngularJS使用的缓存进行更多的自定义控制，可以请求传入一个自定义的缓存实例代替true。

例如，如果要使用LRU（Least Recently Used，最近最少使用）缓存，可以像这样传入缓存实例对象：

	var lru = $cacheFactory('lru',{
	    capacity: 20 //容纳
	});
	// $http请求
	$http.get('/api/users.json', { cache: lru }) 
	.success(function(data){}) 
	.error(function(data){});    

现在，最新的20个请求会被缓存。第21个请求会导致lru从缓存中将时间比较老的请求移除。

通过.config()函数给所有$http请求设置一个默认的缓存：

	angular.module('myApp', [])
	    .config(function($httpProvider, $cacheFactory) {
	        $httpProvider.defaults.cache = $cacheFactory('lru', {
	             capacity: 20
	        }); 
	    });

7、拦截器

什么是拦截器？我们将httpProvider对象输出到控制台

	{
	  "defaults": {
	    "transformResponse": [null],
	    "transformRequest": [null],
	    "headers": {
	      "common": {
	          "Accept": "application/json, text/plain, */*"
	      },
	      "post": {
	          "Content-Type": "application/json;charset=utf-8"
	      },
	      "put": {
	          "Content-Type": "application/json;charset=utf-8"
	      },
	      "patch": {
	          "Content-Type": "application/json;charset=utf-8"
	      }
	    },
	    "xsrfCookieName": "XSRF-TOKEN",
	    "xsrfHeaderName": "X-XSRF-TOKEN",
	    "paramSerializer": "$httpParamSerializer"
	  },
	  "interceptors": [],
	  "$get": ["$httpBackend", "$$cookieReader", "$cacheFactory", "$rootScope", "$q", "$injector", null]
	}
	
可以看到$httpProvider中有一个interceptors数组，而所谓拦截器也就是一个注入到了该数组中的服务。
	
	var app = angular.module('app', []);
	
	app.factory('myInterceptor', function($log) {
	    $log.debug('$log输出啦。。。');
	
	    var myInterceptor = {
	      response: function(rep) {
	        console.log(rep);
	        return rep;
	      }
	    }
	  });
	
	app.config(function($httpProvider) {
	  $httpProvider.interceptors.push('myInterceptor');
	});

当我们在页面中向后端发起http请求时就可以看控制台中输出了我们的调试信息和服务器返回的数据。

关于拦截器的知识，见过的大都是理论上的解释。这里有一个关于angularJS拦截器应用实例的文章：angularjs中的interceptor和挺好的例子

####二、封装$http服务，统一管理请求url

将项目中所有的数据请求统一管理，无疑是一个非常好的开发体验，在以后开发中会借鉴这个思路，加入到自己的项目中。

在实际项目中，我们可以对$http服务作一层封装，比如封装成HttpService，将服务器返回的错误信息进行一次拦截处理，提供统一的错误提示。示例如下：

	angular.module('httpService', [])
	  .service('HttpService', function($http, $q) {
	
	    var service = {
	      get: function(url, config) {
	        return handleRepData('get', url, null, config);
	      },
	      post: function(url, data, config) {
	        return handleRepData('post', url, data, config);
	      },
	      put: function(url, data, config) {
	        return handleRepData('post', url, data, config);
	      },
	      delete: function(url, config) {
	        return handleRepData('delete', url, null, config);
	      }
	    }
	
	    function handleRepData(method, url, data, config) {
	      var promise;
	      var defer = $q.defer();
	      switch (method) {
	        case 'get':
	          promise = $http.get(url, config);
	          break;
	        case 'post':
	          promise = $http.post(url, data, config);
	          break;
	        case 'put':
	          promise = $http.put(url, data, config);
	          break;
	        case 'delete':
	          promise = $http.delete(url, config)
	      }
	
	      promise.then(function(rep) {
	        if (rep.data.success || rep.data.status === 1) {
	          defer.resolve(rep.data);
	        } else {
	          var errorMsg = rep.data.message || '哦，出错啦！但是后端没有给任何信息。';
	          // 弹出错误信息，或者重定向到404页面
	          alert(errorMsg);
	        }
	      }, function() {
	        defer.reject('出错了');
	      })
	
	      return defer.promise;
	    }
	
	    return service;
	  });

将所有的请求url模块划分，统一放置在一个服务下面：

	angular.module('urlService', [])
	  .factory('UrlService', function(HttpService) {
	    var service = {
	      // 数据源
	      cms: {
	        dataSource: {
	          // 新建
	          new: function(params_) {
	            return HttpService.post('/api/datasource/', params_).then(function(data_) {
	              return data_;
	            });
	          },
	          // 更新
	          update: function(params_, name_) {
	            return HttpService.put('/api/datasource/' + name_ +'/', params_).then(function(data_) {
	              return data_;
	            });
	          },
	          // 详情
	          detail: function(params_, name_) {
	            return HttpService.get('/api/datasource/'+ name_ +'/', {params: params_}).then(function(data_) {
	              return data_;
	            });
	          },
	          // 删除
	          delete: function(params_, name_) {
	            return HttpService.delete('/api/datasource/'+ name_ +'/', params_).then(function(data_) {
	              return data_;
	            });
	          },
	          {
	            // ...
	          }
	        }
	      },
	      // 店铺管理
	      store: {
	        shop: {
	          // 新建
	          create: function(params_) {
	            return HttpService.post('/api/shop/create/', params_).then(function(data_) {
	              return data_;
	            });
	          },
	          // 查重
	          checkDuplicate: function(params_) {
	            return HttpService.post('/api/shop/search_duplicate/', params_).then(function(data_) {
	              return data_;
	            });
	          },
	          // 搜索
	          search: function(params_) {
	            return HttpService.get('/api/shop/search/', params_).then(function(data_) {
	              return data_;
	            });
	          },
	          {
	            // ...
	          }
	        }
	      },
	      {
	        // ...
	      }
	    };
	
	    return service;
	  });

最后在控制器中引入urlService服务：

	angular.module('cmsDataSourceController', [])
	  .controller('cmsDataSourcePageCtrl', ['$scope', 'UrlService', function($scope, UrlService) {
	
	    var params = {name: name, alias: alias, cate: cate, conds: conds, tags: tags};
	
	    UrlService.cms.dataSource.new(params).then(function(rep) {
	      tips('数据源创建成功!');
	      $('.btn-update').text('修改数据源');
	    });
	  }]);