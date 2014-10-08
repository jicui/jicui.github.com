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
2 DSL引擎
2 请求处理器和响应绑定

以及外围的诸如

1.冲突处理
2.mock 规格持久
3.搜索策略等 

本文将会对核心的几个模块做一个概要性的介绍，从而达到对我们设计的happymock的一个概括和抽象的目地

Introduction
----
一个简单的dls应该要具备 1 上下文环境，2 结构层次，3 语法关键词 ，4 易于理解 这几个属性
比如我们通过spring 去配置 组件的依赖关系，就是一种spring的 领域特定语言，
还有一种可以应用与dls的是json 格式，例如

{% highlight javascript %}
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
{% endhighlight %}

这个一个很简单的json格式，同时他也完全满足一个dsl所需要的特性，简单，易懂，有语法和层次。

编译器的设计思路
----
dsl的编译器需要满足层次性，关键词判断和子句查找几个特点，比如mock spec 的语法 如上所示，"request",和 "response"是根节点下的两个子节点，而这两个子节点都必须同时存在，才是一个合法的mock
再例如，"body"里面我们可以嵌入 "json","xml"或者"text"，而这三个节点只要出现一个就满足一个正确的 "body"节点的要求，但是如果一个都不出现就不能是一个合法的 "body"节点
针对这样的需求我们可以设计Compiler接口
<img src="/assets/compiler.jpg" height="200px" width="300px" alt="compiler"/>
locateChildNode：可以根据子node的名字来定位，这样的话可以简单的识别出，无效的关键词，比如 用户的误输入把 json 输入成了 jason这样jason这个关键词是找不到对应的childNode的编译器的
compiler:是编译系统的主题，他负责解析传递进入方法体的dsl的规格是否满足以定的条件，比如是否是一个合法的正则又或者是否包含一定数量的字节点，同时负责梳理这些子节点之间的依赖关系
getByKeyword：就是简单的返回当前处理node 所注册的名字。

拥有了这样一个简单的接口，我们就满足了实现一个基于json格式的简单的mock 编译器的功能。同时还具备关键词的可扩展功能。例如，如果我们以后要扩展DSL，在response里面新增"time_delay" ,这个属性
这样我只要修改ResponseCompiler的"locateChildNode"方法，增加一行
{% highlight java %}
 if(keyword.equals("time_delay")){
       return new TimeDelayCompiler();
 }
{% endhighlight %}

就可以方便地实现易于扩展的目标了

DSL引擎
----
<img src="/assets/mock_design.jpg" height="400px" width="600px" alt="AO"/>


前段通过Http的Handler解耦业务代码和协议代码，然后MockRequestHandler发送Mock请求给DSL引擎，DSL引擎内部分解成几个模块

持久层：负责读写mock数据库，通过解析mock 请求来查找mock的配置

组装层：将持久层返回的DSL，更具配置的processor和 binder 来组装成java的pojo

编译层：接受编译的请求，根据请求决定是否需要持久

实体层：持久层返回的一个或者多个MociSetting pojo对象，每个MockSetting都拥有一个match方法
用来判断传入的mock请求是否能满足DSL 在 "request" body 中定义的所有的要求，比如url模式，header字段
cookie 字段，endity body等

对于大规模应用的场景，如果persit返回多个MociSetting ，如何能做到快速查找，例如根据Content-Type的类型，将MociSetting分类等，这块目前还没有在设计中体现到，可以以后再加上

请求处理器和响应绑定
----
请求处理器：它的作用在于当有mock的请求进入DSL引擎内部的时候，请求处理器会依次分析进入的每个request，并且根据定义的匹配原则来匹配request是否满足所有的
预定义的match规则，如果满足则返回相关对应的response配置。但是这里需要考虑一个问题，就是如果一个request同时满足大于一个的MociSetting配置的情况下，DSL按照什么原则来返回mock response.解决这个问题要看需求，不过大致有以下几个思路：

1.返回400 ，让用户调整mock 的配置，或者发送的http请求。

2.返回第一个匹配的请求。

3.按照最优的匹配原则返回。

响应绑定：它的作用在于当请求处理器找到满足需求的mock response的时候，按照预先定义的mock response 规则来绑定相应的http code，http header，cookie等。

总结
---
这篇文章介绍了要设计一个mock DSL引擎需要考虑的几个方面，包括DSL原型，编译器，处理器，绑定器等。
