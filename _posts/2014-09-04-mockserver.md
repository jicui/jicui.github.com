---
layout : post
title : "The value of building mock server"
date : 2014-09-04
comments : true
categories : design
---

The value of building mock server
=====================
inspried by Moco framework

Background
----
Modern application are more based on components design.In one hand,sharding the application 
to pieces can bring more agility to app's scability ,but on the others hand ,it also introduce
dependency problems to qa ,dev and performance tester.

Introduction
----
Imagine that you are a automation qa that writing a piece of automation test case to test the api
behavior,thus until development deploy their code to testing environment ,qa has no way to test 
and run their automation code to ensure the automation function is done.

Imagine that you are going to test a 3rd paryty response payload ,that claims "a little" update on their
response payload,and you want to ensure our system can work as expected,how to test that?

Imagine that you are are front end engineer that develops a single page + 
restful application ,how to been unblocked for the frontend work ,and meanwhile backend's 
coding work is still on going?

Imagine that you are a backend engineer,and you are going to create a service and internally 
it has dependency to other component,they has their seperate SDLC,how can you test you function 
based on api specs even other component's api is not ready yet.

Imagine that you are a performance tester,you are going to test the real wold api performance 
benchmark and this api has 3rd party dependency,like generate UPS shipping label via RPC call.how can you 
test the performance in our own code as well as to maxisum the situation in production.

The answer is mock server.


defining mock specs 
------
How to define the mock specs depends on the question ,"are we really need to merge our testing 
date and testing code together?".It depends.

For automation ,as eventually they will remove the mock server to real api endpoint ,thus mock server 
is just a tempority solution for them to be unblocked ,apparently no need to maintain the mock specs

For performance tests and manully function test,it is all handful work ,no need to code for setting up 
any mock specs as well

in above two cases ,a UI tooling may sufficient for them to go ahead. like below:

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

if in bound request has cookies["login"] equals to true and uri is "/foo" ,then return text "success".


But for develop's integration test,it definitely needs to maintain their mock specs as part of 
their integration tests,so as to ensure a more safety and standalone environment to have a fine-grain
control of their internal code logic.The reason is they are testing their own code logic instead of other 
component's,thus they need to set up various specs to cover positive ,negative,failover,
retry and so on ,to ensure their code is robust enough to cover all aspects in distributing environment.

in above case ,a native language friends dsl may be a good choice for way to go,like below:

{% highlight java %}
whenRequest().matchUri("/foo").and().matchCookies("login",true).
thenReturn().text("success");

{% endhighlight %}

Moci system leverage the same DLS as moco framework does ,so all the DLS details please go to 


