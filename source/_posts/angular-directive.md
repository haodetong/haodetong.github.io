---
title: angularJS中的指令directive
date: 2017-09-14 09:30:09
tags: angular
category: angularjs
keywords: angular, angularjs
description: angularJS中的指令directive

---

原文：

[AngularJS 指令实践指南（一）](http://blog.jobbole.com/62249/)

[AngularJS 指令实践指南（二）](http://blog.jobbole.com/62999/)

#### 一、创建自定义指令

	var app = angular.module('myapp', []);
	
	app.directive('rabbitHeader', function() {
	  return {
	      restrict: 'AE',
	      replace: 'true',
	      template: '<h3>Hello World!!</h3>'
	  };
	});
	
##### 1、表现形式

* html元素名

		<rabbit-header></rabbit-header>

* 元素的属性

		<div rabbit-header></div>
		
* 类名		
	
		<div class="rabbit-header"></div>	
		
* 注释

		<!-- directive: rabbit-header -->
		
##### 2、属性

*restrict:*

* A：只限属性使用

* C：只限类名使用

* E：只限元素名使用

* M：只限注释使用

*template | templateUrl*

* template: 不一定要是简单的字符串，可以包含其它指令或表达式{} 等

* templateUrl: 指向模板的一个地址

*replace*

* true: 生成的html内容会替换掉定义此指令的html

* false: 生成的html内容会插入到定义此指令的元素中

##### 3、Link函数和Scope

* 默认情况下指令不会创建新的子scope。

* 如果指令存在于一个controller下，它会使用这个contoller的scope。

* 指令的scope在link函数中使用。

index.html

	<body ng-controller="MainCtrl">
	  <input type="text" ng-model="color" placeholder="Enter a color" />
	  <hello-world></hello-world>
	</body>
	
app.js

	app.directive('helloWorld', function() {
	  return {
	    restrict: 'AE',
	    replace: true,
	    template: '<p style="background-color:{{color}}">Hello World',
	    link: function(scope, elem, attrs) {
	      elem.bind('click', function() {
	        elem.css('background-color', 'white');
	        scope.$apply(function() {
	          scope.color = "white";
	        });
	      });
	      elem.bind('mouseover', function() {
	        elem.css('cursor', 'pointer');
	      });
	    }
	  };
	});

link函数中的三个参数：

* scope - 在这个例子中，指令的scope就是controller的scope。

* ele - 指令的jQLite(jQuery的子集)包装DOM元素。如果你在引入AngularJS之前引入了jQuery，那么这个元素就是jQuery元素，而不是jQLite元素。由于这个元素已经被jQuery/jQLite包装了，所以我们就在进行DOM操作的时候就不需要再使用 $()来进行包装。

* attr - 一个包含了指令所在元素的属性的标准化的参数对象。举个例子，你给一个HTML元素添加了一些属性：，那么可以在 link 函数中通过 attrs.someAttribute 来使用它。

link函数主要用来为DOM元素添加事件监听、监视模型属性变化、以及更新DOM。

在上面的指令代码片段中，我们添加了两个事件， click，和 mouseover。click 处理函数用来重置的背景色，而 mouseover 处理函数改变鼠标为 pointer。在模板中有一个表达式 {color}，当父scope中的 color 发生变化时，它用来改变 Hello World 文字的背景色。 这个plunker演示了这些概念

##### 4、compile函数

compile 函数在 link 函数被执行之前用来做一些DOM改造。它接收下面的参数：

* tElement – 指令所在的元素
* attrs – 元素上赋予的参数的标准化列表

要注意的是 compile 函数不能访问 scope，并且必须返回一个 link 函数。但是如果没有设置 compile 函数，你可以正常地配置 link 函数，（有了compile，就不能用link，link函数由compile返回）。compile函数可以写成如下的形式：

	app.directive('test', function() {
	  return {
	    compile: function(tElem,attrs) {
	      //do optional DOM transformation here
	      return function(scope,elem,attrs) {
	        //linking function here
	      };
	    }
	  };
	});
	
大多数的情况下，你只需要使用 link 函数。这是因为大部分的指令只需要考虑注册事件监听、监视模型、以及更新DOM等，这些都可以在 link 函数中完成。 但是对于像 ng-repeat 之类的指令，需要克隆和重复 DOM 元素多次，在 link 函数执行之前由 compile 函数来完成。这就带来了一个问题，为什么我们需要两个分开的函数来完成生成过程，为什么不能只使用一个？要回答好这个问题，我们需要理解指令在Angular中是如何被编译的！

##### 5、指令是如何被编译的	

1、编译阶段 compile

* angular应用启动后，$compile服务遍历所有DOM元素。

* 所有指令都被识别后，angular执行他们的compile方法。

* compile 方法返回一个 link 函数，被添加到稍后执行的 link 函数列表中。

2、链接阶段 linking

* 所有收集到的link函数都将被一一执行。

* 指令创造出来的模板会在正确的scope下被解析和处理，然后返回具有事件响应的真实的DOM节点。

##### 6、改变指令的Scope

默认情况下，指令获取它父节点的controller的scope。

如果将父controller的scope暴露给指令，那么他们可以随意地修改 scope 的属性。

在某些情况下，你的指令希望能够添加一些仅限内部使用的属性和方法。但是如果我们在父的scope中添加，会污染父scope。

所以我们可以选择以下两种方式：

* 一个子scope – 这个scope原型继承子父scope。

		app.directive('helloWorld', function() {
		  return {
		    scope: true,  // use a child scope that inherits from parent
		    restrict: 'AE',
		    replace: 'true',
		    template: '<h3>Hello World!!</h3>'
		  };
		});
		
* 一个隔离的scope – 一个孤立存在不继承自父scope的scope。

		app.directive('helloWorld', function() {
		  return {
		    scope: {},  // use a new isolated scope
		    restrict: 'AE',
		    replace: 'true',
		    template: '<h3>Hello World!!</h3>'
		  };
		});
		
这个指令使用了一个隔离的scope。

隔离的scope在我们想要创建可重用的指令的时候是非常有好处的。

通过使用隔离的scope，我们能够保证我们的指令是自包含的，可以被很容易的插入到HTML应用中。

它内部不能访问父的scope，所保证了父scope不被污染。

在我们的 helloWorld 指令例子中，如果我们将 scope 设置成 {}，那么上面的代码将不会工作。 它会创建一个新的隔离的scope，那么相应的表达式 { color } 会指向到这个新的scope中，它的值将是 undefined.

使用隔离的scope并不意味着我们完全不能访问父scope的属性。其实有一些技术可以允许我们访问父scope的属性，甚至监视他们的变化。


#### 二、隔离scope与父scope通信

##### 1、隔离scope与父scope之间的数据绑定

假设我们已经初始化完成app这个变量所指向的Angular模块。那么我们的 helloWorld 指令如下面代码所示：

	app.directive('helloWorld', function() {
	  return {
	    scope: {},
	    restrict: 'AE',
	    replace: true,
	    template: '<p style="background-color:{{color}}">Hello World</p>',
	    link: function(scope, elem, attrs) {
	      elem.bind('click', function() {
	        elem.css('background-color','white');
	        scope.$apply(function() {
	          scope.color = "white";
	        });
	      });
	      elem.bind('mouseover', function() {
	        elem.css('cursor', 'pointer');
	      });
	    }
	  };
	});
	
使用这个指令的HTML标签如下：

	<body ng-controller="MainCtrl">
	  <input type="text" ng-model="color" placeholder="Enter a color"/>
	  <hello-world/>
	</body>

上面的代码现在是不能工作的。因为我们用了一个隔离的scope，指令内部的 {color} 表达式被隔离在指令内部的scope中(不是父scope)。

但是外面的输入框元素中的 ng-model 指令是指向父scope中的 color 属性的。

所以，我们需要一种方式来绑定隔离scope和父scope中的这两个参数。

在Angular中，这种数据绑定可以通过为指令所在的HTML元素添加属性和在指令定义对象中配置相应的 scope 属性来实现。

让我们来细究一下建立数据绑定的几种方式。

##### 选择一：使用 @ 实现单向文本绑定

在下面的指令定义中，我们指定了隔离scope中的属性 color 绑定到指令所在HTML元素上的参数 colorAttr。

在HTML标记中，你可以看到 {color}表达式被指定给了 color-attr 参数。当表达式的值发生改变时，color-attr 参数也跟着改变。隔离scope中的 color 属性的值也相应地被改变。

	app.directive('helloWorld', function() {
	  return {
	    scope: {
	      color: '@colorAttr'
	    },
	    ....
	    // the rest of the configurations
	  };
	});

更新后的HTML标记代码如下：

	<body ng-controller="MainCtrl">
	  <input type="text" ng-model="color" placeholder="Enter a color"/>
	  <hello-world color-attr="{{color}}"/>
	</body>

我们称这种方式为单项绑定，是因为在这种方式下，你只能将字符串(使用表达式)传递给参数。

当父scope的属性变化时，你的隔离scope模型中的属性值跟着变化。你甚至可以在指令内部监控这个scope属性的变化，并且触发一些任务。

然而，反向的传递并不工作。你不能通过对隔离scope属性的操作来改变父scope的值。

注意点：
当隔离scope属性和指令元素参数的名字一样时，你可以以更简单的方式设置scope绑定：

	app.directive('helloWorld', function() {
	  return {
	    scope: {
	      color: '@'
	    },
	    ....
	    // the rest of the configurations
	  };
	});

相应使用指令的HTML代码如下：

<hello-world color="{{color}}"/>

##### 选择二：使用 = 实现双向绑定

让我们将指令的定义改变成下面的样子：

	app.directive('helloWorld', function() {
	  return {
	    scope: {
	      color: '='
	    },
	    ....
	    // the rest of the configurations
	  };
	});

相应的HTML修改如下：

	<body ng-controller="MainCtrl">
	  <input type="text" ng-model="color" placeholder="Enter a color"/>
	  <hello-world color="color"/>
	</body>

与 @ 不同，这种方式让你能够给属性指定一个真实的scope数据模型，而不是简单的字符串。

这样你就可以传递简单的字符串、数组、甚至复杂的对象给隔离scope。

同时，还支持双向的绑定。每当父scope属性变化时，相对应的隔离scope中的属性也跟着改变，反之亦然。

和之前的一样，你也可以监视这个scope属性的变化。

##### 选择三：使用 & 在父scope中执行函数

有时候从隔离scope中调用父scope中定义的函数是非常有必要的。为了能够访问外部scope中定义的函数，我们使用 &。

比如我们想要从指令内部调用 changColor() 方法。下面的代码告诉我们该怎么做：

	app.directive('helloWorld',function(){
	  return{
	    scope:{
	      color:'=',
	      changeColor:'&',
	    },
	    ...
	  }
	});

相应的HTML代码如下：

	<body ng-controller="MainCtrl">
	    <input type="text" ng-model="color" placeholder="Enter a color"/>
	    <hello-world color="color" change-color='change()'/>
	</body>
	
这个 [Plunker](http://plnkr.co/edit/k4scWKwtGBJw7lfKGqVJ?p=preview) 例子对上面的概念做了很好的诠释。

##### 2、父scope、子scope以及隔离scope的区别

下面的这个原则也许可以帮助你为你的指令选择正确的scope。

* 父scope(scope: false) – 这是默认情况。如果你的指令不操作父scoe的属性，你就不需要一个新的scope。这种情况下是可以使用父scope的。

* 子scope(scope: true) – 这会为指令创建一个新的scope，并且原型继承自父scope。如果你的指令scope中的属性和方法与其他的指令以及父scope都没有关系的时候，你应该创建一个新scope。在这种方式下，你同样拥有父scope中所定义的属性和方法。

* 隔离scope(scope:{}) – 这就像一个沙箱！当你创建的指令是自包含的并且可重用的，你就需要使用这种scope。你在指令中会创建很多scope属性和方法，它们仅在指令内部使用，永远不会被外部的世界所知晓。如果是这样的话，隔离的scope是更好的选择。隔离的scope不会继承父scope。

#### 三、Transclusion（嵌入）

Transclusion可以让我们的指令包含任意内容的方法。

如果你在指令定义中设置 transclude:true，一个新的嵌入的scope会被创建，它原型继承自父scope。

如果你想要你的指令使用隔离的scope，但是它所包含的内容能够在父scope中执行，transclusion也可以帮忙。

##### 1、demo

假设我们注册一个如下的指令：

	app.directive('outputText', function() {
	  return {
	    transclude: true,
	    scope: {},
	    template: '<div ng-transclude></div>'
	  };
	});

它使用如下：

	<div output-text>
	  <p>Hello {{name}}</p>
	</div>
	
在这个例子中DOM内容 <p>Hello {name}</p> 被提取和放置到 <div ng-transclude></div> 内部。

表达式{name}所对应的属性是在父scope中被定义的，而非子scope。

你可以在这个 [Plunker](http://plnkr.co/edit/YQBPB9cvGgJiYMuIjzTw?p=preview) 例子中做一些实验。

##### 2、transclude:’element’ 和 transclude:true的区别

有时候我我们要嵌入指令元素本身，而不仅仅是它的内容。在这种情况下，我们需要使用 transclude:’element’。

它和 transclude:true 不同，它将标记了 ng-transclude 指令的元素一起包含到了指令模板中。

使用transclusion，你的link函数会获得一个名叫 transclude 的链接函数，这个函数绑定了正确的指令scope，并且传入了另一个拥有被嵌入DOM元素拷贝的函数。

你可以在这个 transclude 函数中执行比如修改元素拷贝或者将它添加到DOM上等操作。 类似 ng-repeat 这样的指令使用这种方式来重复DOM元素。

仔细研究一下这个Plunker，它使用这种方式复制了DOM元素，并且改变了第二个实例的背景色。

同样需要注意的是，在使用 transclude:’element’的时候，指令所在的元素会被转换成HTML注释。

所以，如果你结合使用 transclude:’element’ 和 replace:false，那么指令模板本质上是被添加到了注释的innerHTML中——也就是说其实什么都没有发生！

相反，如果你选择使用 replace:true，指令模板会替换HTML注释，那么一切就会如果所愿的工作。

使用 replade:false 和 transclue:’element’有时候也是有用的，比如当你需要重复DOM元素但是并不想保留第一个元素实例（它会被转换成注释）的情况下。对这块还有疑惑的同学可以阅读stackoverflow上的这篇讨论，介绍的比较清晰。

#### 四、controller 函数和 require

如果想让指令与指令交互，就要用到controller函数。比如有些情况下，你需要通过组合两个指令来实现一个UI组件。那么你可以通过如下的方式来给指令添加一个 controller 函数。

	app.directive('outerDirective', function() {
	  return {
	    scope: {},
	    restrict: 'AE',
	    controller: function($scope, $compile, $http) {
	      // $scope is the appropriate scope for the directive
	      this.addChild = function(nestedDirective) { // this refers to the controller
	        console.log('Got the message from nested directive:' + nestedDirective.message);
	      };
	    }
	  };
	});

这个代码为一个名叫 outerDirective的指令添加了controller。

当另一个指令 innerDirective 想要与指令outerDirective交互时，innterDirective 需要声明对 outerDirective 的 controller 实例的引用(require)。

可以通过如下的方式实现：

	app.directive('innerDirective', function() {
	  return {
	    scope: {},
	    restrict: 'AE',
	    require: '^outerDirective',
	    link: function(scope, elem, attrs, controllerInstance) {
	      //the fourth argument is the controller instance you require
	      scope.message = "Hi, Parent directive";
	      controllerInstance.addChild(scope);
	    }
	  };
	});

相应的HTML代码如下：

	<outer-directive>
	  <inner-directive></inner-directive>
	</outer-directive>

require: ‘^outerDirective’ 告诉Angular在元素以及它的父元素outerDirective中搜索controller。

这样被找到的 controller 实例会作为第四个参数controllerInstance被传入到 link 函数中。

在我们的例子中，我们将嵌入的指令的scope发送给父亲指令。

如果你想尝试这个代码的话，请在开启浏览器控制台的情况下打开这个[Plunker](http://plnkr.co/edit/NMWGE6l9p1tBZh3jCfKn?p=preview)。

同时，[这篇Angular官方文档](https://docs.angularjs.org/guide/directive)上的最后部分给了一个非常好的关于指令交互的例子，是非常值得一读的。

#### 五、一个记事本的应用

这一部分，我们使用Angular指令创建一个简单的记事本应用。我们会使用HTML5的 localStorage 来存储笔记。

我们会创建一个展现记事本的指令。

用户可以查看他/她创建过的笔记记录。当他点击 add new 按钮的时候，记事本会进入可编辑状态，并且允许创建新的笔记。当点击 back 按钮的时候，新的笔记会被自动保存。笔记的保存使用了一个名叫 noteFactory 的工厂类，它使用了 localStorage。

你可以从[GitHub](https://github.com/jsprodotcom/source/blob/master/AngularJS_Note_Taker-source_code.zip)上下到这个Demo的源代码。

#### 六、总结

一个很重要的点需要注意的是，任何使用jQuery能做的事情，我们都能用Angular指令来做到，并且使用更少的代码。

所以，在使用jQuery之前，请考虑一下我们能否在不进行DOM操作的情况下以更好的方式来完成任务。试着使用Angular来最小化jQuery的使用吧。