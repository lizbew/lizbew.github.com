---
layout: post
title: "试读webpy template源码"
description: ""
category: 
tags: [python, webpy, template]
---
{% include JB/setup %}

这几天花了点时间读了下web.py的template代码，只是大概的过了下。主要想了解下Python中AST编译相关的模块使用方法。

先看个webpy template使用方法，来自其[cookbook](webpy.org/docs/0.3/templetor). 首先是template内容的html, 置于_./templates/hello.html_

{% highlight html %}
$def with (name)
Hello $name!
{% endhighlight %}

在代码里初始化render, 传递文件夹名为参数，然后就可以调用和模板同名的方法了，生成的html以字符串返回。

{% highlight python %}
render = web.template.render('templates')
print render.hello('world')
{% endhighlight %}

web.py template的语法格式同样参考其cookbook，变量赋值或语句都以$开始；$if/$for/$while都需要单独一行，内容行首遵循python的缩进格式。template内容是一行行处理的， 生成nodelist，转换成python代码，然后再调用python 编译服务。

看代码当然是从[github](https://github.com/webpy/webpy/blob/master/web/template.py)开始了， template 相关的代码在文件_ webpy / web / template.py
_. template的初始化是从@class Render@开始，由@render._load_template@,模板类是@class BaseTemplate@和@class Template@。模板的解析工作由@class Parser@进行， 解析后生成的node类有DefwithNode，TextNode，ExpressionNode，AssignmentNode,LineNode，BlockNode，ForNode，CodeNode，StatementNode，IfNode，ElseNode，ElifNode，DefNode，VarNode，SuiteNode。每个node类都定义有@emit()@用于生成对应的python代码。

明天再看下将python code编译的具体细节。


