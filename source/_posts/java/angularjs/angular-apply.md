---
title: 理解数据绑定过程 - `$watch`, `$apply`和`$digest`

date: 2017-09-14 09:54:51

tags: angular

category: angularjs

keywords: angular, angularjs

description: 理解数据绑定过程 - `$watch`, `$apply`和`$digest`

---

项目开发过程中，在自定义指令里，用jquery捕捉到事件后改变scope里的变量，模板里的变量没有发生变化。幸运的是，从中文社区找到一篇关于数据绑定过程的文章，不仅解决了这个问题，对angular的理解也更加深了一层。

博文地址：[理解$watch ，$apply 和 $digest — 理解数据绑定过程](http://www.angularjs.cn/A0a6)

英文地址：[$watch How the $apply Runs a $digest](http://angular-tips.com/blog/2013/08/watch-how-the-apply-runs-a-digest/)

#### 一、浏览器循环事件和Angular.js扩展

我们的浏览器一直在等待事件，比如用户交互。假如你点击一个按钮或者在输入框里输入东西，事件的回调函数就会在javascript解释器里执行，然后你就可以做任何DOM操作，等回调函数执行完毕时，浏览器就会相应地对DOM做出变化。 Angular拓展了这个事件循环，生成一个<font color="#d44950">angular context</font>的执行环境（记住，这是个重要的概念），为了解释什么是<font color="#d44950">context</font>以及它如何工作，我们还需要解释更多的概念。

#### 二、$watch队列（$watch list）

页面上绑定一个model就会向$watch队列里插入一条 $watch 。$watch 检测它监视的model是否发生了变化。

##### 1、demo

<font color="#d44950">index.html</font>

	User: <input type="text" ng-model="user" />
	Password: <input type="password" ng-model="pass" />
	
页面绑定了两个scope的变量，所以$watch list里面加入了两个$watch。

##### 2、demo

<font color="#d44950">controllers.js</font>

	app.controller('MainCtrl', function($scope) {
	  $scope.foo = "Foo";
	  $scope.world = "World";
	});
	
<font color="#d44950">index.html</font>

	Hello, {{ World }}

这里，即便我们在$scope上添加了两个东西，但是只有一个绑定在了UI上，因此在这里只生成了一个$watch.

##### 3、demo

<font color="#d44950">controllers.js</font>

	app.controller('MainCtrl', function($scope) {
	  $scope.people = [...];
	});
	
<font color="#d44950">index.html</font>

	<ul>
	  <li ng-repeat="person in people">
	      {{person.name}} - {{person.age}}
	  </li>
	</ul>
	
这里又生成了多少个$watch呢？每个person有两个（一个name，一个age），然后ng-repeat又有一个，因此10个person一共是(2 * 10) +1,也就是说有21个$watch。

##### 4、总结

因此，每一个绑定到了UI上的数据都会生成一个$watch。

那这些$watch是什么时候生成的呢？

当我们的模版加载完毕时，也就是在linking阶段（Angular分为compile阶段和linking阶段—译者注），Angular解释器会寻找每个directive，然后生成每个需要的$watch。

#### 三、$digest循环

还记得我前面提到的扩展的事件循环吗？当浏览器接收到可以被angular context处理的事件时，$digest循环就会触发

这个循环是由两个更小的循环组合起来的：

一个处理evalAsync队列。

另一个处理$watch队列，这个也是本篇博文的主题。 这个是处理什么的呢？$digest将会遍历我们的$watch

* 嘿，$watch，你的值是什么？

	* 是9。

* 好的，它改变过吗？

	* 没有，先生。

*（这个变量没变过，那下一个）

* 你呢，你的值是多少？

	* 报告，是Foo。

* 刚才改变过没？

	* 改变过，刚才是Bar。

* （很好，我们有DOM需要更新了）

* 继续询问直到$watch队列都检查过。

这就是所谓的dirty-checking。

既然所有的$watch都检查完了，那就要问了：有没有$watch更新过？如果有至少一个更新过，这个循环就会再次触发，直到所有的$watch都没有变化。这样就能够保证每个model都已经不会再变化。

记住如果循环超过10次的话，它将会抛出一个异常，防止无限循环。

当$digest循环结束时，DOM相应地变化。

##### 1、demo

<font color="#d44950">controllers.js</font>

	app.controller('MainCtrl', function() {
	  $scope.name = "Foo";
	
	  $scope.changeFoo = function() {
	      $scope.name = "Bar";
	  }
	});

<font color="#d44950">index.html</font>

	{{ name }}
	<button ng-click="changeFoo()">Change the name</button>
	
这里我们只有一个$watch，因为ng-click不生成$watch（函数是不会变的）。

* 按下按钮

* 浏览器接收到一个事件，进入angular context（后面会解释为什么）。

* $digest循环开始执行，查询每个$watch是否变化。

* 由于监视$scope.name的$watch报告了变化，它会强制再执行一次$digest循环。

* 新的$digest循环没有检测到变化。

* 浏览器拿回控制权，更新与$scope.name新值相应部分的DOM。

#### 四、通过$apply进入angular context

谁来决定什么事件可以进入angular context，而哪些又不能进入呢？$apply！

* 如果当事件触发时，你调用$apply，它会进入angular context，如果没有调用就不会进入。

* 现在你可能会问：刚才的例子里我也没有调用$apply啊，为什么？Angular为了做了！

* 当你点击带有ng-click的元素时，事件就会被封装到一个$apply调用。

* 例如你有一个ng-model=”foo”的输入框，然后你敲一个f，事件就会这样调用$apply(“foo = ‘f’;”)。

#### 五、Angular什么时候不会自动为我们$apply呢？

这是Angular新手共同的痛处。为什么我的jQuery不会更新我绑定的东西呢？因为jQuery没有调用$apply，事件没有进入angular context，$digest循环永远没有执行。

##### 1、demo

<font color="#d44950">app.js</font>

	app.directive(‘clickable’, function() {
	
	return {
	  restrict: "E",
	  scope: {
	    foo: '=',//通过 '=' 双向绑定，隔离的scope得以与controller里的父scope通信
	    bar: '='//双向绑定
	  },
	  template: '<ul style="background-color: lightblue"><li>{{foo}}</li><li>{{bar}}</li></ul>',
	  link: function(scope, element, attrs) {
	    element.bind('click', function() {
	      scope.foo++;
	      scope.bar++;
	    });
	  }
	}
	
	});
	
	app.controller('MainCtrl', function($scope) {
	  $scope.foo = 0;
	  $scope.bar = 0;
	});

代码意图：将foo和bar从controller里绑定到一个list里面，每次点击这个元素的时候，foo和bar都会自增1。

但是当我们点击元素ul时，发现元素没有变化还是0。

这是因为点击事件是一个没有封装到$apply里的事件。虽然如此，但是scope里的变量（foo和bar）确实是自增了的，只是因为没能通过$apply进入angular context，所以模板里的dom没能得到更新。

也就是说，如果我们自己执行一次$apply，那么这些$watch就会看到变化，然后根据需要更新dom。

试试看看这个例子：[http://jsbin.com/opimat/2/](http://jsbin.com/opimat/2/)

* 当我们先点击ul时，像上面所讲的，虽然scope里的foo和bar都自增了，但由于element.bind(‘clilck’)事件没有封装到$apply里，所以dom没有得到更新，模板里看不到变化。

* 但当我们点击 change hello按钮时，由于ng-click事件被封装到了$apply里，所以进入了angular context里，$digest循环执行，询问每个$watch时，发现不仅$sopce.hello的值发生了变化，scope.foo和scope.bar也发生了变化。

* 浏览器更新dom时，就可以看到三处变化了。

##### 2、demo

现在你在想那并不是你想要的，你想要的是点击蓝色区域的时候就更新点击数。

a、很简单，执行一下$apply就可以了：

	element.bind('click', function() {
	  scope.foo++;
	  scope.bar++;
	
	  scope.$apply();
	});

$apply是我们的$scope（或者是direcvie里的link函数中的scope）的一个函数，调用它会强制一次$digest循环（除非当前正在执行循环，这种情况下会抛出一个异常，这是我们不需要在那里执行$apply的标志）。

试试看：[http://jsbin.com/opimat/3/edit](http://jsbin.com/opimat/3/edit)

b、有用啦，但是有一种更好的使用$apply的方法：

	element.bind('click', function() {
	  scope.$apply(function() {
	      scope.foo++;
	      scope.bar++;
	  });
	})

有什么不一样的？差别就是在第一个版本中，我们是在angular context的外面更新的数据，如果有发生错误，Angular永远不知道。很明显在这个像个小玩具的例子里面不会出什么大错，但是想象一下我们如果有个alert框显示错误给用户，然后我们有个第三方的库进行一个网络调用然后失败了，如果我们不把它封装进$apply里面，Angular永远不会知道失败了，alert框就永远不会弹出来了。

#### 六、使用 $watch 来监视你自己的东西

你已经知道了我们设置的任何绑定都有一个它自己的$watch，当需要时更新DOM，但是我们如果要自定义自己的watches呢？简单

##### 1、demo

<font color="#d44950">app.js</font>

	app.controller('MainCtrl', function($scope) {
	  $scope.name = "Angular";
	
	  $scope.updated = -1;
	
	  $scope.$watch('name', function() {
	    $scope.updated++;
	  });
	});
	
<font color="#d44950">index.html</font>

	<body ng-controller="MainCtrl">
	  <input ng-model="name" />
	  Name updated: {{updated}} times.
	</body>

这就我们创造的一个新的$watch方法。

* 第一个参数是一个字符串或函数，在这里只是一个字符串，就是我们要监视的变量的名字。

* 第二个参数是一个回调函数，当变量发生变化时被调用。

* 我们要知道的第一件事就是当controller执行到这个 $watch 时，它会立即执行一次，困此我们设置updated为 -1 。

试试看：[http://jsbin.com/ucaxan/1/edit](http://jsbin.com/ucaxan/1/edit)

##### 2、demo

<font color="#d44950">app.js</font>

	app.controller('MainCtrl', function($scope) {
	  $scope.name = "Angular";
	
	  $scope.updated = 0;
	
	  $scope.$watch('name', function(newValue, oldValue) {
	    if (newValue === oldValue) { return; } // AKA first run
	    $scope.updated++;
	  });
	});
	
<font color="#d44950">index.html</font>

	<body ng-controller="MainCtrl">
	  <input ng-model="name" />
	  Name updated: {{updated}} times.
	</body>

* watch的第二个参数接受两个参数，新值和旧值。

* 我们可以用他们来略过第一次的执行。

*  通常你不需要略过第一次执行，但在这个例子里面你是需要的。灵活点嘛少年。

##### 3、demo

<font color="#d44950">app.js</font>

	app.controller('MainCtrl', function($scope) {
	  $scope.user = { name: "Fox" };
	
	  $scope.updated = 0;
	
	  $scope.$watch('user', function(newValue, oldValue) {
	    if (newValue === oldValue) { return; }
	    $scope.updated++;
	  });
	});
	
<font color="#d44950">index.html</font>

	<body ng-controller="MainCtrl">
	  <input ng-model="user.name" />
	  Name updated: {{updated}} times.
	</body>
	
我们想要监视$scope.user对象里的任何变化，和以前一样这里只是用一个对象来代替前面的字符串。

试试看：[http://jsbin.com/ucaxan/3/edit](http://jsbin.com/ucaxan/3/edit)

* 没用，为啥？

* 因为$watch默认是比较两个对象所引用的是否相同，在例子1和2里面，每次更改$scope.name都会创建一个新的基本变量，因此$watch会执行，因为对这个变量的引用已经改变了。

* 在上面的例子里，我们在监视$scope.user，当我们改变$scope.user.name时，对$scope.user的引用是不会改变的，我们只是每次创建了一个新的$scope.user.name，但是$scope.user永远是一样的。

##### 4、demo

<font color="#d44950">app.js</font>

	app.controller('MainCtrl', function($scope) {
	  $scope.user = { name: "Fox" };
	
	  $scope.updated = 0;
	
	  $scope.$watch('user', function(newValue, oldValue) {
	    if (newValue === oldValue) { return; }
	    $scope.updated++;
	  }, true);
	});
	
<font color="#d44950">index.html</font>

	<body ng-controller="MainCtrl">
	  <input ng-model="user.name" />
	  Name updated: {{updated}} times.
	</body>
	
试试看：[http://jsbin.com/ucaxan/4/edit](http://jsbin.com/ucaxan/4/edit)

* 现在有用了吧！因为我们对$watch加入了第三个参数，它是一个bool类型的参数，表示的是我们比较的是对象的值而不是引用。

* 由于当我们更新$scope.user.name时$scope.user也会改变，所以能够正确触发。

#### 七、总结

1、好吧，我希望你们已经学会了在Angular中数据绑定是如何工作的。我猜想你的第一印象是dirty-checking很慢，好吧，其实是不对的。它像闪电般快。但是，是的，如果你在一个模版里有2000-3000个watch，它会开始变慢。但是我觉得如果你达到这个数量级，就可以找个用户体验专家咨询一下了

2、无论如何，随着ECMAScript6的到来，在Angular未来的版本里我们将会有Object.observe那样会极大改善$digest循环的速度。同时未来的文章也会涉及一些tips&tricks。

3、另一方面，这个主题并不容易，如果你发现我落下了什么重要的东西或者有什么东西完全错了，请在github上提交问题或提交修正。

#### 八、回到总题

总结：数据绑定机制：

1、angular发现一个model就会向$watch list里插入一个$watch，用来监测这个model是否发生了变化 。

2、当事件触发后，比如ng-click，事件就会被封装到一个$apply。$apply决定了哪些事件触发后可以进入angular context，哪些事件触发后不会进入到angular context。

3、$apply调用后，进入到angular context。

4、$digest循环执行，询问每个$watch，看看它监测的model有没有发生变化。

5、有变化再循环，直到没有发现变化为止。

6、浏览器拿回控制权，更新相应的dom。

这也就解释了为什么jquery改变scope的变量时，为什么dom没有更新。因为jQuery没有调用$apply，事件没有进入angular context，$digest循环永远没有执行。
