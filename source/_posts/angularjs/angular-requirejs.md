---
title: angularjs + requirjs前端架构
date: 2017-05-17 15:52:11
tags: angular
category: angularjs
keywords: angular, requirejs
description: angularjs + requirjs前端架构

---

在决定使用angularJS开发项目时，考虑到多人协作，模块化，扩展等等因素，决定引入requirJS实现js文件的异步加载，解决模块之间的依赖关系。

参考了一些关于angularJS项目的前端架构，都觉得不甚理想，幸运的是最终看到一篇关于angularJS如何集成requireJS的文章：

原文地址：[如何将angularJS项目与requireJS集成](http://www.cnblogs.com/cunjieliu/p/4021121.html)

一、项目组织目录：

- css 文件夹

- img 文件夹

- js 文件夹

 -- appIndex 文件夹

  --- api文件夹

  --- controller 文件夹

     ---- ctlModule.js

     ---- mainController.js

     ---- homeController.js

  --- directives 文件夹

  --- services 文件夹

  --- app.js

  --- index_main.js

  --- router.js

 -- appLogin 文件夹

  --- …

  --- 与appIndex类似

  --- …

- lib 文件夹

 -- angular 文件夹

  --- angular.js

  --- angular-ui-router.js

  --- …

 -- bootstrap 文件夹

 -- jquery 文件夹

 -- require 文件夹

 -- php 文件夹

 -- template 文件夹

  --- common 文件夹

  --- tplIndex 文件夹

  --- tplLogin文件夹

- 404.html

- favicon.ico

- humans.txt

- index.html

- LICENSE.txt

- login.html

- server.js

二、组织目录说明

两个大的模块，一个appIndex，代表前台展示模块；一个appLogin，代表登录模块。appLogin与appIndex类似，以appIndex为例：

appIndex下的文件夹：

api: 与后端交互

controllers： 控制器

directives: 指令

services: 服务

appIndex下的文件：

app.js 定义整个项目的module

index_main.js 配置脚本文件，启动angular

router.js 定义路由

api下的文件：

apiModule.js 定义api的module

mainApi.js 加载所有的api文件

homeApi.js 一个普通的api文件

controllers下的文件：

ctlModule.js 定义控制器的module

mainController.js 加载所有的控制器文件

homeController.js 一个普通的控制器文件

directives下的文件：

dirModule.js 定义指令的module

mainDirective.js 加载所有的指令文件

headerDirective.js 一个普通的指令文件

services下的文件：

serModule.js 定义服务的module

mainService.js 加载所有的服务文件

homeService.js 一个普通的服务文件

三、命名约定

$stateProvider.state('home',{
    url: '/home',
   templateUrl:'../../template/tplIndex/home.html',
   controller:'homeCtl'
})
以homeController为例：

对应的模板为home.html

控制器名字：homeCtl

return ctl.controller(‘homeCtl’,function($scope,person,serGetTitle,testData){

 ... 
});
})

homeCtl里使用的服务，看到ser开头的，如serGetTitle，对应到homeService.js里查找，看到Data结尾的，如testData，对应到homeApi.js里查找
四、代码分析

1、index.html 主要代码如下：

<!doctype html>
<html class="no-js" lang="">
    <head>
        <script data-main="js/appIndex/index_main" src="lib/require/require.js"></script>
    </head>
    <body>
        <rabbit-header></rabbit-header>

        <div>
            <ul>
                <li><a href="#/home">home</a></li>
            </ul>
        </div>

        <div ui-view></div>

        <rabbit-footer></rabbit-footer>

    </body>
</html>
在首页index.html只需要引入requireJs库文件，并且指明入口函数main.js，采用手动启动angularjs应用，所以这里不用为首页添加ng-app=’rabbit’。

2、index_main.js 主要代码如下：

require.config({
    baseUrl:'js',
    paths:{
        jquery:'../lib/jquery/jquery-1.9.1',
        angular:'../lib/angular/angular',
        angularUiRouter:'../lib/angular/angular-ui-router',
        Restangular:'../lib/angular/restangular-1.5.2',
        lodash:'../lib/angular/lodash-1.8.3',
        app:'appIndex/app',
        router:'appIndex/router',
        mainController:'appIndex/controllers/mainController',
        ctlModule:'appIndex/controllers/ctlModule',
        homeController:'appIndex/controllers/homeController',
        ...
    },
    shim:{
        'angular':{
            exports:'angular'
        },
        'angularUiRouter':{
            deps: ['angular'],
            exports:'angularUiRouter'
        },
        ...
    },
    urlArgs:"bust=" + (new Date()).getTime()  //防止读取缓存，调试用
});

require([
    'jquery',
    'angular',
    'angularUiRouter',
    'app',
    'router'
], function ($,angular,angularUiRouter,app,router) {
   $(document).ready(function(){
        //采用手动启动angularjs应用，所以这里不用为首页添加ng-app=’rabbit’
        angular.bootstrap(document, ['rabbit']); 
    });
});
3、两个重要的文件app.js和router.js

app.js 主要代码如下：

define(["angular",'mainController','mainDirective','mainService','mainApi'],function(angular){
        return angular.module("rabbit",['ui.router','app.ctlModule','app.dirModule','app.serModule','app.apiModule']);
    })
定义匿名模块:依赖的mainController和mainDirective主要是用来加载angular的控制器和指令

ui.router:一个基于ngRoute开发的第三方路由，作为框架额外的附加功能，它们都将以模块依赖的形式被引入

rabbit为自定义的模块名，依赖第三方路由模块ui.router,以及自定义的控制器模块，自定义的指令模块

这样做的目的是：在程序启动(bootstrap)的时候，加载依赖模块(如：ui.router)，将所有挂载在该模块的服务(provider)，指令(directive)，过滤器(filter)等都进行注册，那么在后面的程序中便可以调用了。

router.js 主要代码如下：

define(['app'],function(app){
        return app.run(['$rootScope', '$log', function($rootScope, $log){
                        $rootScope.$on('$stateChangeSuccess', function(event, toState, toParams, fromState, fromParams){
                            $log.debug('successfully changed states') ;
                        });
                    }])
                  .config(function($stateProvider, $urlRouterProvider, $locationProvider, $uiViewScrollProvider){
                        // 默认进入重定向
                        //$urlRouterProvider.otherwise('/index');
                        $stateProvider.state('home',{
                            url: '/home',
                            templateUrl:'../../template/tplIndex/home.html',
                            controller:'homeCtl'
                        })                       
                })
});
配置了一个指定 /home 的路由。

4、控制器

mainController.js 主要代码如下：

define(['homeController','indexController'], function() {});
主要用来加载各个控制器（所有的控制器都将在这个文件中被加载），我们可以有很多个控制器文件，按照具体需要进行添加。

homeController.js 主要代码如下：

define(['ctlModule','jquery'],function(ctl,$){
    return ctl.controller('homeCtl',function($scope,serGetTitle,testData){
        $scope.title = serGetTitle.title;
        $scope.projects = testData.getMessages();
    })
})
控制器homeCtl依赖于ctlModule模块，其实可以理解为app.ctlModule模块下的一个“方法”。

ctlModule.js 主要代码如下：

define(['angular'], function (angular) {
    'use strict';
    return angular.module('app.ctlModule', []);
 });
声明一个叫作app.ctlModule的模块，app.js里的模块rabbit，依赖于这个模块，并调用这个模块下的所有“方法”

5、指令，服务，接口

同控制器的定义方法完全类似。

五、说明

php文件夹放了一些php文件，可以用apache建一个站点，模拟与服务器端的数据交互。

server.js是一个静态文件服务器，mac下进入目录，执行node server，即可创建一个简单的nodejs服务器。

个人感觉这样设计一个前端架构，虽然会让开发变得繁琐，但功能划分比较明确，提供了一个开发标准，适合于多人协同作业，总体上来说利大于弊。

源码下载地址：angularJS集成requireJS