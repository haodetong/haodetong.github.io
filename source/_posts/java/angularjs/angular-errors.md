---
title: angular应用中常见问题记录
date: 2017-09-14 09:23:54
tags: angular
category: angularjs
keywords: angular, angularjs
description: angular应用中常见问题记录

---

##### 1、问题：Template for directive ‘rabbitFooter’ must have exactly one root element

控制台报错：angular.js?bust=1463908350577:13550 Error: [$compile:tplrt] Template for directive ‘rabbitFooter’ must have exactly one root element. ../../../template/tplIndex/rabbitFooter.html

指令rabbitFooter对应的模板：
	
	<!--s footer-->
	<div class="footerbox">
	    <div class="footercon">
	        ...
	    </div>
	    <div class="copybox">
	        ...
	    </div>
	    <!--
	    <div class="authpics">
	        ...
	    </div>-->
	</div>
	<!--e footer-->
	
解决方案：保证模板的所有代码要有一个最外层的DIV，最外层注释也不要有。

	<div class="footerbox">
	    <div class="footercon">
	        ...
	    </div>
	    <div class="copybox">
	        ...
	    </div>
	    <!--
	    <div class="authpics">
	        ...
	    </div>
	    -->
	</div>