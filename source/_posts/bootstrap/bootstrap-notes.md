---
title: bootstrap笔记

date: 2017-09-14 10:21:19

tags: bootstrap

category: bootstrap

keywords: bootstrap

description: bootstrap常用笔记

---

[Bootstrap](http://v3.bootcss.com/)：最受欢迎的HTML、CSS和JS框架，用于开发响应式布局、移动设备优先的WEB项目。

#### 启步

1、响应式布局标签：

	<meta name="viewport" content="width=device-width, initial-scale=1">

2、为了让最新浏览器运行在最新的渲染模式下，建议将此 <meta> 标签加入到页面中：

	<meta http-equiv="X-UA-Compatible" content="IE=edge">
	
3、让部分国产浏览器如360采用高速模式渲染页面：

	<meta name="renderer" content="webkit">

#### 全局CSS样式

##### 一、概览

1、移动设备优先

在移动设备浏览器上，通过为视口（viewport）设置 meta 属性为 user-scalable=no 可以禁用其缩放（zooming）功能。

	<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">

2、布局容器

.container 类用于固定宽度并支持响应式布局的容器。

	<div class="container">
	  ...
	</div>
	
.container-fluid 类用于 100% 宽度，占据全部视口（viewport）的容器。

	<div class="container-fluid">
	  ...
	</div>

##### 二、栅格系统

1、媒体查询

Less文件中使用以下媒体查询（media query）来创建关键的分界点阈值。

	/* 超小屏幕（手机，小于 768px） */
	/* 没有任何媒体查询相关的代码，因为这在 Bootstrap 中是默认的（还记得 Bootstrap 是移动设备优先的吗？） */
	
	/* 小屏幕（平板，大于等于 768px） */
	@media (min-width: @screen-sm-min) { ... }
	
	/* 中等屏幕（桌面显示器，大于等于 992px） */
	@media (min-width: @screen-md-min) { ... }
	
	/* 大屏幕（大桌面显示器，大于等于 1200px） */
	@media (min-width: @screen-lg-min) { ... }

2、栅格参数

	| 超小屏幕 手机（<768px） 
	| 小屏幕 平板（>=768px） 
	| 中等屏幕 显示器（>=992px） 
	| 大屏幕 大显示器（>=1200px）
	
.container 最大宽度  | 自动 | 750px | 970px | 1170px
------------- | ------------- | ------------- | ------------- | -------------
类前缀 | .col-xs- | .col-sm- | .col-md- | .col-lg-
槽宽 | 30px（每列左右各15px）| 30px | 30px | 30px

实例：使用单一的一组 .col-md-* 栅格类，就可以创建一个基本的栅格系统，在手机和平板设备上一开始是堆叠在一起的（超小屏幕到小屏幕这一范围），在桌面（中等）屏幕设备上变为水平排列。所有“列（column）必须放在 ” .row 内。

![.col-md](../../public/css/images/column.png)

<center>grid demo</center>

	<div class="row">
	  <div class="col-md-1">.col-md-1</div>
	  <div class="col-md-1">.col-md-1</div>
	  <div class="col-md-1">.col-md-1</div>
	  <div class="col-md-1">.col-md-1</div>
	  <div class="col-md-1">.col-md-1</div>
	  <div class="col-md-1">.col-md-1</div>
	  <div class="col-md-1">.col-md-1</div>
	  <div class="col-md-1">.col-md-1</div>
	  <div class="col-md-1">.col-md-1</div>
	  <div class="col-md-1">.col-md-1</div>
	  <div class="col-md-1">.col-md-1</div>
	  <div class="col-md-1">.col-md-1</div>
	</div>
	<div class="row">
	  <div class="col-md-8">.col-md-8</div>
	  <div class="col-md-4">.col-md-4</div>
	</div>
	<div class="row">
	  <div class="col-md-4">.col-md-4</div>
	  <div class="col-md-4">.col-md-4</div>
	  <div class="col-md-4">.col-md-4</div>
	</div>
	<div class="row">
	  <div class="col-md-6">.col-md-6</div>
	  <div class="col-md-6">.col-md-6</div>
	</div>

3、实例：移动设备和桌面屏幕

是否不希望在小屏幕设备上所有列都堆叠在一起？那就使用针对超小屏幕和中等屏幕设备所定义的类吧，即 .col-xs- 和 .col-md-。请看下面的实例，研究一下这些是如何工作的。

	<!-- Stack the columns on mobile by making one full-width and the other half-width -->
	<div class="row">
	  <div class="col-xs-12 col-md-8">.col-xs-12 .col-md-8</div>
	  <div class="col-xs-6 col-md-4">.col-xs-6 .col-md-4</div>
	</div>
	
	<!-- Columns start at 50% wide on mobile and bump up to 33.3% wide on desktop -->
	<div class="row">
	  <div class="col-xs-6 col-md-4">.col-xs-6 .col-md-4</div>
	  <div class="col-xs-6 col-md-4">.col-xs-6 .col-md-4</div>
	  <div class="col-xs-6 col-md-4">.col-xs-6 .col-md-4</div>
	</div>
	
	<!-- Columns are always 50% wide, on mobile and desktop -->
	<div class="row">
	  <div class="col-xs-6">.col-xs-6</div>
	  <div class="col-xs-6">.col-xs-6</div>
	</div>

4、列偏移

使用 .col-md-offset- 类可以将列向右侧偏移。这些类实际是通过使用 选择器为当前元素增加了左侧的边距（margin）。例如，.col-md-offset-4 类将 .col-md-4 元素向右侧偏移了4个列（column）的宽度。

	<div class="row">
	  <div class="col-md-4">.col-md-4</div>
	  <div class="col-md-4 col-md-offset-4">.col-md-4 .col-md-offset-4</div>
	</div>
	<div class="row">
	  <div class="col-md-3 col-md-offset-3">.col-md-3 .col-md-offset-3</div>
	  <div class="col-md-3 col-md-offset-3">.col-md-3 .col-md-offset-3</div>
	</div>
	<div class="row">
	  <div class="col-md-6 col-md-offset-3">.col-md-6 .col-md-offset-3</div>
	</div>

5、列排序

通过使用 .col-md-push- 和 .col-md-pull- 类就可以很容易的改变列（column）的顺序。

	<div class="row">
	  <div class="col-md-9 col-md-push-3">.col-md-9 .col-md-push-3</div>
	  <div class="col-md-3 col-md-pull-9">.col-md-3 .col-md-pull-9</div>
	</div>

##### 三、排版

1、标题

h1到h6做为主标题 samll做为副标题

	<h1>h1. Bootstrap heading <small>Secondary text</small></h1>

2、对齐

	<p class="text-left">Left aligned text.</p>
	<p class="text-center">Center aligned text.</p>
	<p class="text-right">Right aligned text.</p>
	<p class="text-justify">Justified text.</p>
	<p class="text-nowrap">No wrap text.</p>

3、地址

	<address>
	  <strong>Twitter, Inc.</strong><br>
	  795 Folsom Ave, Suite 600<br>
	  San Francisco, CA 94107<br>
	  <abbr title="Phone">P:</abbr> (123) 456-7890
	</address>

	<address>
	  <strong>Full Name</strong><br>
	  <a href="mailto:#">first.last@example.com</a>
	</address>
	
4、内联列表

	<ul class="list-inline">
	  <li>...</li>
	</ul>

5、水平排列的描述

	<dl class="dl-horizontal">
	  <dt>...</dt>
	  <dd>...</dd>
	</dl>
	
四、表格

1、响应式表格

将任何 .table 元素包裹在 .table-responsive 元素内，即可创建响应式表格，其会在小屏幕设备上（小于768px）水平滚动。当屏幕大于 768px 宽度时，水平滚动条消失。

	<div class="table-responsive">
	  <table class="table">
	    ...
	  </table>
	</div>
五、表单

1、基本实例

单独的表单控件会被自动赋予一些全局样式。所有设置了 .form-control 类的 `<input>`、`<textarea>` 和 `<select>` 元素都将被默认设置宽度属性为 width: 100%;。 将 label 元素和前面提到的控件包裹在 .form-group 中可以获得最好的排列。

	<form>
	  <div class="form-group">
	    <label for="exampleInputEmail1">Email address</label>
	    <input type="email" class="form-control" id="exampleInputEmail1" placeholder="Email">
	  </div>
	  <div class="form-group">
	    <label for="exampleInputPassword1">Password</label>
	    <input type="password" class="form-control" id="exampleInputPassword1" placeholder="Password">
	  </div>
	  <div class="form-group">
	    <label for="exampleInputFile">File input</label>
	    <input type="file" id="exampleInputFile">
	    <p class="help-block">Example block-level help text here.</p>
	  </div>
	  <div class="checkbox">
	    <label>
	      <input type="checkbox"> Check me out
	    </label>
	  </div>
	  <button type="submit" class="btn btn-default">Submit</button>
	</form>

2、…