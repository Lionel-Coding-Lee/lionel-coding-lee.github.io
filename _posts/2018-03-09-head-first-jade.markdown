---
layout: post
title:  "JADE奇妙旅行"
date:   2018-03-09 09:36:54 +0800
categories: jade
---

# WHAT IS JADE
JADE的中文含义是玉石，一般是指翡翠，同时有一种名为JADE的面向对象编程语言，而这里我们要了解的是另外一种JADE：

> Jade is a terse language for writing HTML templates.
> Jade是一种用来编写HTML模板的简洁的语言

这种运行在Node上的模板引擎有三个特性：

1. Produces HTML
2. Supports dynamic code
3. Supports reusability (DRY)

同时一个容易忽略的细节是：jade是js的，这么说或许不清楚，换句话说jade是可以访问js作用域的


# JADE, HTML, CSS
## WHAT IS HTML
html文件随处可见，我们浏览的网页99.99%都是html，但HTML到底是啥

> 超文本标记语言（英语：HyperText Markup Language，简称：HTML）是一种用于创建网页的标准标记语言

实际上，浏览器是一个功能非常强大的程序，或者可以说是一个平台platform，html可以认为是在浏览器这个平台上的可执行文件

html往往是静态的，通过DOM将元素组织成树状结构，浏览器对html中的元素进行解析和可视化

## WHAT IS CSS
当html定义的元素越来越多，很难针对每一个元素去指定表现规格，比如字体粗度，有无边框等，这时候就需要【样式】来统一地批量解决这个问题

> CSS，层叠样式表 (Cascading Style Sheets，缩写为CSS），是一种样式表语言，用来描述HTML或XML（包括如SVG、XHTML之类的XML分支语言）文档的呈现

而当同一个元素不止被一个样式定义的时候，在树状结构上离该元素最近的那个样式生效

## JADE 2 HTML
Jade其实就是提供了另一种语法，来更简洁的表达html的元素，同样可以使用css定义的样式，同时能完成不同jade内容的拼接和简单逻辑变量的访问

## 突然想起JSP
其实Web前端一般分为框架+引擎，jade就是一种引擎，负责生成静态的html，而框架，比如angularJs，react等，负责控制逻辑

而J！S！P！这种笔者之前唯一正规写过的“前端”，其实把框架和引擎的事情都干了，使用java作为控制逻辑的脚本

不过现在好像凉了，可能不仅仅是因为JavaScript的成熟，更多的应该是生成html时，jsp其实很痛苦……很多时候都是直接声明html元素来生成


# JADE语法

