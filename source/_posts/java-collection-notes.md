---
title:  集合框架三 - 查阅技巧
date: 2018-01-09 14:42:46
tags: collecionsFramework
category: java
keywords: java, api
description: java集合查阅技巧

---

### 三、集合框架-集合查阅的技巧

唯一性？

-- 需要唯一：Set
	
---- 需要顺序？

------ 需要： TreeSet

------ 不需要： HashSet

------ 和存储一致的顺序：LinkedHashSet

-- 不需要唯一：List

---- 频繁增删？

----- 需要：LinkedList

------ 不需要：ArrayList

结构体系：

List

|-- ArrayList

|-- LinkedList

Set 

|-- HashSet

|-- TreeSet

后缀名就是该集合所属的体系。

前缀名就是该集合的数据结构。

看到array：就要想到数组，查询快，有角标。

看到Link：就要想到增删快，想到add get remove+first last的方法。

看到hash：就要想到hash表，唯一性，元素需要覆盖hashCode方法和equals方法。

看到tree：就要想到二叉树，排序，两个接口Compareble和Comparator。

而且这些常用的集合容器都是不同步的。
