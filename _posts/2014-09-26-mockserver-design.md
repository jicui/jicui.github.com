---
layout : post
title : "The mock engine 的设计思路"
date : 2014-09-26
comments : true
categories : design
---
如何设计一个mock server DSL 引擎
=====================
inspried by Moco framework

Background
----
设计一个mockserver 的后台引擎需要考虑到几个核心模块

1 编译器
2 请求处理器
3 响应绑定

以及外围的诸如

1.冲突处理
2.mock 规格持久
3.搜索策略等 

本文将会对各个方面做一个概要性的介绍，从而达到对我们设计的happymock的一个概括和抽象的目地

Introduction
----
一个简单的dls应该要具备 1 上下文环境，2 结构层次，3 语法关键词 ，4 易于理解 这几个属性
比如我们通过spring 去配置 组件的依赖关系，就是一种spring的 领域特定语言，
还有一种可以应用与dls的是json 格式，例如

{% highlight javascript %}
```
{
  "request" :
    {
      "uri" : "/foo",
      "cookies" :
        {
          "login" : "true"
        }
    },
  "response" :
    {
      "body":{
        "text" : "success"
      }
    }
}
```
{% endhighlight %}

这个一个很简单的json格式，同时他也完全满足一个dsl所需要的特性，简单，易懂，有语法和层次。

编译器的设计思路
----



Conclusion
----




