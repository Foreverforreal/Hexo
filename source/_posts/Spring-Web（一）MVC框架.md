title: Spring Web（一）MVC框架
id: 1501315692211
author: 不识
tags:
  - Spring
categories:
  - Spring
date: 2017-07-29 16:08:00
---
[原文链接](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#mvc)
***
# Spring Web MVC框架简介
***
Spring Web模型-视图-控制器（MVC）框架是围绕一个DispatcherServlet设计的，它将请求分派给处理器（handler），Spring MVC具有可配置的处理器映射器，视图解析器，本地化，时区和主题解析以及上传文件支持。默认的handler是基于@Controller和@RequestMapping注解，提供了广泛的灵活处理方法。随着Spring 3.0的引入，@Controller机制还允许您通过@PathVariable注解和其他功能创建RESTful Web站点和应用程序。

<div style="border: 1px solid #ccc;background-color:#f8f8f8;padding:20px;">“开放扩展...”在Spring Web MVC和Spring中的一个关键设计原则是“开放扩展，关闭修改”原则。<br/>
Spring Web MVC的核心类中的一些方法被标记为final。作为开发人员，您不能覆盖这些方法来提供自己的行为。这并不是随意设计的，而是特别考虑到这个原则。<br/>
有关这个原理的解释，请参考Seth Ladd的Expert Spring Web MVC和Web Flow;具体参见第一版第117页的“A Look At Design”一节。或者，参见
- [Bob Martin, The Open-Closed Principle (PDF)](https://www.cs.duke.edu/courses/fall07/cps108/papers/ocp.pdf)<br/>
当您使用Spring MVC时，您不能向final方法添加advice 。例如，您不能向AbstractController.setSynchronizeOnSession()方法添加advice 。有关AOP代理的更多信息，以及为什么不能向final方法添加advice ，请参阅第11.6.1节“[了解AOP代理”](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#aop-understanding-aop-proxies)。
</div> 
在Spring Web MVC中，您可以使用任何对象作为命令或表单支持对象;您不需要实现框架特定的接口或基类。Spring的数据绑定时高度灵活的：例如，它将类型不匹配视为可由应用程序评估的验证错误，而不是系统错误。因此，你不需要将你的业务对象的属性复制为简单的无类型的字符串，仅用于处理无效提交，或者用于正确转换字符串。相反，它通常最好直接绑定到您的业务对象。

Spring的视图解析非常灵活。Controller通常负责准备一个带有数据和选择的视图名称的模型Map，但它也可以直接写入响应流并完成请求。视图名称解析是高度可配置它，通过文件拓展或Accept header content type negotiation，通过bean名称，一个属性文件，或甚至一个ViewResolver实现。这个模型（MVC中的M）是一个Map接口，它允许对视图技术的完全抽象。您可以直接与基于模板的渲染技术（如JSP，Velocity和Freemarker）集成，或直接生成XML，JSON，Atom和许多其他类型的内容。模型Map简单地被转换成适当的格式，如JSP请求属性，Velocity模板模型。      

## Spring Web MVC的特点
***


<div style="border: 1px solid #ccc;background-color:#f8f8f8;padding:20px;">**Spring Web Flow**
Spring Web Flow（SWF）旨在成为管理Web应用程序页面流的最佳解决方案。
</div>迭代