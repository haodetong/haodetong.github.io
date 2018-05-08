---
title: 第一章 spring概述
keywords: spring
description: spring概述
date: 2018-05-04 14:33:18
category: spring
tags: spring

---

#### 1.1 概述

spring的核心是控制反转（IOC）和面向切面编程（AOP）。

spring的主要作用就是为代码“解耦”，降低代码间的耦合度。

spring根据代码功能特点，将降低耦合度的方式分为了两类：IOC与AOP。
IOC使得主业务在相互调用过程中，不用再自己维护关系了，即不用再自己创建使用的对象了。而是由spring容器统一管理，自动“注入”。而AOP使得系统级服务得到了最大复用，且不用由程序员手工将系统级服务“混杂”到主业务逻辑中了，而是由spring容器统一完成“织入”。



