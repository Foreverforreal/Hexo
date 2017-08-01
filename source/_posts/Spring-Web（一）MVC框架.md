title: Spring Web（一）MVC框架
id: 1501315692211
author: 不识
tags:
  - Spring
categories:
  - Spring
date: 2017-07-29 16:08:00
---
[原文链接](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#mvc)：参考文档的这一部分涵盖了Spring框架对表示层（特别是基于Web的演示层）的支持，包括在Web应用程序中支持WebSocket风格的消息传递。

Spring Framework自己的Web框架，Spring Web MVC，在前两章中有介绍。随后的章节涉及Spring框架与其他Web技术（如JSF）的集成。

接下来是Spring Framework的MVC portlet框架。

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
Spring Web Flow（SWF）旨在成为管理Web应用程序页面流的最佳解决方案。<br/>
在Servlet和Portlet环境中，SWF与Spring MVC和JSF等现有框架集成。如果您有一个业务流程会受益于会话模型而不是纯粹的请求模型，那么SWF可能是这个的解决方案。<br/>
SWF允许你捕获逻辑页面流作为自包含模块，以便可以在不同场景中重用，因此非常适合通过controller导航来引导用户驱动业务流程的Web应用程序模块。
</div>
Spring的Web模块包含许多独特的Web支持功能：
- *清晰的角色分离*。每个角色 — 控制器，验证器，命令对象，表单对象，模型对象，DispatcherServlet，处理器映射器，视图解析器等等 - 可以由专门的对象来实现。
- *框架和作为JavaBeans的应用程序类的强大而直观的配置。*此配置功能包括跨上下文的简单引用，例如从Web控制器到业务对象和验证器。
- *适应性，非侵入性和灵活性。*定义任何你需要的控制器方法签名，可能使用给定方案的参数注解之一（例如@RequestParam，@RequestHeader，@PathVariable等等）。
- *可重用的业务代码，不需要复制。*使用现有的业务对象作为命令或表单对象，而不是镜像它们来继承特定的框架基类。
- *可定制的绑定和验证。*类型不匹配作为应用程序级验证错误，保持违规值，本地化日期和数字绑定等，而不是对只使用仅包含字符串的表单对象进行手动解析和转换为业务对象。
- *可定制的处理器映射和视图解析。*处理器映射和视图解析策略的范围从简单的基于URL的配置到复杂的专用解析策略。Spring比那些需要特定技术的Web MVC框架更加灵活。
- *灵活的模型传递。*拥有name/value的模型传递Map支持域任何视图技术的轻松集成。
- *可定制的区域设置，时区和主题解析，支持带有或不带有Spring标签库的JSP，支持JSTL，支持Velocity，无需额外的桥接等。*
- *一个简单而强大的JSP标签库，被称为Spring标签库，为数据绑定和主题等功能提供支持。*自定义标签在标记代码方面允许具有最大的灵活性。有关标签库描述符的信息，请参见附录标题为[第43章，spring JSP标签库](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#spring-tld)
- *在Spring 2.0中引入的JSP表单标签库，使得在JSP页面中的写入表单更容易。*有关标签库描述符的信息，请参见附录标题为[Chapter 44，spring-form JSP Tag Library](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#spring-form-tld)
- *Bean的生命周期作用域在当前的HTTP请求或HTTP Session中*这不是Spring MVC本身的一个特定功能，而是Spring MVC使用的WebApplicationContext容器。这些bean域在第7.5.4节[“Request, session, global session, application和 WebSocket域”](/2017/07/25/Spring-核心技术（一）IoC容器/#Request, session, global session, application和 WebSocket域
)中描述

## 其他MVC实现的可插拔性
***
对于某些项目，非Spring MVC实现更为可取。许多团队希望利用他们在现有的技能和工具上的投资，例如使用JSF。

如果你不想使用Spring的Web MVC，但打算利用Spring提供的其他解决方案，你可以轻松地将Web MVC框架与你选择的Spring集成。通过其ContextLoaderListener简单地启动一个Spring根应用程序上下文，并通过任何action对象中的ServletContext属性（或Spring的各自的帮助方法）访问它。没有涉及“插件”，因此不需要专门的集成。从Web层的角度来看，你只是将Spring作为库使用，将根应用程序上下文实例作为入口点。

即使不使用Spring Web MVC,注册bean和Spring的Service也是唾手可得的。在这种情况下，Spring不会与其他Web框架竞争。它只是解决了纯Web MVC框架没有解决的许多事情，从bean配置到数据访问和事务处理。所以你可以使用Spring中间层和/或数据访问层来丰富您的应用程序，即使您只想使用JDBC或Hibernate的事务抽象。