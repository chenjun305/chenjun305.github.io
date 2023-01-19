---
layout: post
title: Mybatis的mapper文件中$和#的区别
date: 2019-01-15 10:28:21
description: 
tags: java
categories: programming
---

一般来说，我们使用mybatis generator来生成mapper.xml文件时，会生成一些增删改查的文件，这些文件中需要传入一些参数，传参数的时候，我们会注意到，参数的大括号外面，有两种符号，一种是#，一种是$。这两种符号有什么区别呢？

{% highlight sql %}
SELECT * FROM employee WHERE name=#{name}

SELECT * FROM employee ORDER BY ${salary}
{% endhighlight %}

从上面的内容我们可以比较清楚的看到，一般#{}用于传递查询的参数，一般用于从dao层传递一个string或者其他的参数过来，mybatis对这个参数会进行加引号的操作，将参数转变为一个字符串。

比如，这边我们想根据姓名查询某个人的信息，我们会从dao传一个参数，比如jack过来，mybatis生成对应的sql为：

{% highlight sql %}
SELECT * FROM employee WHERE name="jack"
{% endhighlight %}

而$则不同，我们一般用于ORDER BY的后面。此时mybatis对这个参数不会进行任何的处理，直接生成sql语句。例如，此处我们传入salary作为参数，传入第二个中，此时，mybatis生成的sql语句为：

{% highlight sql %}
SELECT * FROM employee ORDER BY salary
{% endhighlight %}

可以看到，mybatis对其没有做任何的处理。

但是，我们一般推荐使用的是#{}，不使用${}的原因如下：

会引起sql注入，因为${}会直接参与sql编译
会影响sql语句的预编译，因为 ${ } 仅仅为一个纯碎的 string 替换，在动态 SQL 解析阶段将会进行变量替换
这是一个比较小的知识点，但是也容易为遗忘，所以记录在此。
