title: Spring（一）概述
id: 1500863364622
author: 不识
tags:
  - Spring
categories:
  - Spring
date: 2017-07-24 10:29:00
---
Spring Framework是一个轻量级解决方案，也是构建企业级应用程序的潜在一站式服务。但是，Spring是模块化的，允许你只使用你所需的部分，而无需引用其他的部分。你可以在任何web框架的顶层使用IOC容器，但也可以仅使用 Hibernate integration code或 JDBC abstraction layer。Spring框架支持声明式事务管理，通过RMI或web service远程访问您的业务逻辑，以及用于数据持久化的各种选项。它提供了一个全功能的MVC框架，使您能够将AOP透明地集成到您的软件中。

Spring被设计为非侵入式的，这意味着你的域逻辑代码通常不需要依赖框架本身。在你的集成层（比如数据访问层）中，将存在对数据访问技术和Spring库的一些依赖。但是，应该很容易将这些依赖关系与您的代码库的其余部分隔离开来。
***
# Spring入门
***
本参考指南提供有关Spring框架的详细信息。它为所有功能提供了全面的文档，以及Spring的一些基本概念（如“依赖注入”）的一些背景知识。

如果您刚开始使用Spring，则可能需要通过创建基于Spring Boot的应用程序来开始使用Spring Framework。Spring Boot提供一种创建一个基于Spring生成就绪应用程序的快捷（主观上）方式。它基于Spring框架，有利于配置的约定，并且旨在尽可能快地开始运行。

您可以使用[start.spring.io](http://start.spring.io/)生成一个基本项目，或按照“[入门指南](https://spring.io/guides)”中的一个指导，如“[开始构建RESTful Web服务](https://spring.io/guides/gs/rest-service/)”。除了易于学习以外，这些指南非常重视任务，它们大部分都是基于Spring Boot。它们还包含了当解决特定问题时你可能想要考虑的Spring解决方案中的其他项目。
***
# Spring框架简介
***
Spring Framework是一个Java平台，为开发Java应用程序提供全面的基础框架支持。Spring处理这些基础框架，以便你可以专注于你的应用程序。

Spring使您能够从“plain old Java objects”（POJO）构建应用程序，并将企业服务非侵入式应用于POJO。此功能适用于全部的Java SE编程模型以及部分Java EE。

作为应用程序开发人员，您可以从Spring平台中获益：
- 使Java方法在数据库事务中执行，而不必处理事务API。
- 使本地Java方法成为HTTP端点，而无需处理Servlet API。
- 使本地Java方法成为消息处理器，而无需处理JMS API。
- 使本地Java方法成为管理操作，而无需处理JMX API。

## 依赖注入和控制反转
***
一个Java应用程序—这是一个宽泛的术语，它的范围从受限的嵌入式应用程序到n层的服务端企业应用程序—通常由相互协作来形成应用程序的对象组成。因此，应用程序中的对象彼此有依赖关系。

虽然Java平台提供了丰富的应用程序开发功能，但是它缺乏将基本构建块组织一个连贯整体的方法，它将将该任务留给架构师和开发人员。虽然你可以使用设计模式，如 Factory, Abstract Factory, Builder, Decorator和Service Locator来组成构成应用程序的各种类和对象实例，这些模式简单地：最佳实践给了一个名字，描述了模式的作用，应用于哪里，它解决的问题等等。设计模式是形式化的最佳实践，你必须在应用程序中自己实现。

Spring框架控制反转-Inversion of Control（IoC）组件通过提供一种形式化的方式，将不同的组件组合成一个可以随用完全工作的应用程序，来解决这个问题。Spring框架将形式化的设计模式作为头等对象进行编写，这样你可以集成到你自己的应用程序中。许多组织和机构以这种方式使用Spring框架来设计强大的，可维护的应用程序。

> **背景知识**  
"问题是，哪一方面的控制（他们）反转了？"Martin Fowler在2004年在他的网站上提出了关于控制反转（IoC）的问题.Fowler建议重新命名，使其更加自明，并提出了*依赖注入*这个名称。

## 框架模块
***
Spring框架由被组织成约20个模块的功能组成。这些模块被分组为核心容器（Core Container），数据访问/集成（Data Access/Integration），Web，AOP（面向切面编程），Instrumentation，消息和测试，如下表所示。
![Spring框架概览](/images/spring/spring-overview.png.pagespeed.ce.XVe1noRCMt.png)
以下部分列出了每个功能的可用模块和它的artifact名称及其涵盖的主题。Artifact名称与依赖关系管理工具中使用的*artifact ID*相关。

### 核心容器
核心容器由***spring-core***，***spring-beans***，***spring-context***，***spring-context-support*** 和***spring-expression***（Spring表达式语言）模块组成。

***spring-core*** 和***spring-beans*** 模块提供框架的基本部分，包括IoC和依赖注入功能。***BeanFactory*** 是工厂模式的一个复杂实现。它消除了对单例编程的需要，并允许您将依赖关系的配置和说明与实际程序逻辑分离。

Context(***spring-context*** )模块在Core and Beans模块提供的实体基础上构建：它是以框架式方式访问对象的一种手段，这类似于JNDI注册。Context模块从Beans模块继承其功能，并且添加了对国际化的支持（例如使用资源束），事件传播，资源加载以及通过例如Servlet的容器透明地创建上下文。Context模块还支持Java EE功能，如EJB，JMX和基本远程处理。***ApplicationContext*** 接口是Context模块的焦点。***spring-context-support*** 提供了将常见的第三方库集成到Spring应用程序上下文中的支持，用于缓存（EhCache, Guava, JCache），邮件（JavaMail），作业调度（CommonJ, Quartz）以及模板引擎（FreeMarker, JasperReports, Velocity）。

***spring-expression*** 模块提供了强大的表达式语言，用于在运行时查询和操作对象图。它是JSP 2.1规范中规定的统一表达语言（统一EL）的扩展。该语言支持设置和获取属性值，属性分配，方法调用，访问数组，集合和索引器的内容，逻辑和算术运算符，命名变量以及从Spring的IoC容器中以名称检索对象。它还支持列表投影与选择以及常见列表聚合。

### AOP和Instrumentation

***spring-aop*** 模块提供了符合AOP联盟的面向方面的编程实现，允许您定义方法拦截器和切入点，来将应分离的功能代码完全解耦。使用源代码级元数据功能，您还可以以类似于.NET属性的方式将行为信息合并到代码中。

单独的***spring-aspects*** 模块提供与AspectJ的集成。

***spring-instrument*** 模块提供了在某些应用服务器中使用的类仪表支持和类加载器实现。***spring-instrument-tomcat*** 模块包含Spring的Tomcat Instrumentation代理。

### 消息
Spring Framework 4包括一个***spring-messaging*** 模块，其中包含来自Spring Integration项目（如Message，MessageChannel，MessageHandler等）的关键摘要，以及一些其他的作为基于消息的应用程序的基础。这个模块还包含一系列用于映射消息到方法的注解，类似于Spring MVC基于注解的编程模型。

### 数据访问/集成
数据访问/集成层由JDBC,ORM,OXM,JMS和Transaction模块组成。

***spring-jdbc*** 模块提供了一个JDBC抽象层，它不需要进行繁琐的JDBC编程和解析数据库供应商特定的错误代码。

***spring-tx*** 模块对于实现特殊接口和所有POJO（简单Java对象）的类，支持支持编程和声明式事务管理。

***spring-orm*** 模块对流行的对象关系映射API，包括 JPA, JDO和Hibernate等提供了整合层. 使用这个模块你可以将所有的这些O/R映射框架与Spring提供的所有其他功能结合使用，比如前面提到的简单的声明式事务管理功能。

***spring-oxm*** 模块提供了一个支持Object/XML映射实现的抽象层，如JAXB，Castor，XMLBeans，JiBX和XStream。

***spring-jms***（Java Messaging Service）包含用于生成和消费消息的功能。自Spring Framework 4.1以来，它提供了与***spring-messaging***模块的集成。


### Web
Web层由spring-web，spring-webmvc,spring-websocket和spring-webmvc-portlet模块组成。

***spring-web*** 模块提供了基本的面向Web的集成功能，例如多部分文件上传功能，使用Servlet监听器和面向Web应用程序上下文来初始化IoC容器。它还包含一个HTTP客户端和Spring的远程支持的Web相关部分。

***spring-webmvc*** 模块（也称为Web-Servlet模块）包含Web应用程序的Spring的模型-视图-控制器（MVC）和REST Web Service实现。Spring的MVC框架在域模型代码和Web表单之间提供了清晰的分隔，并与Spring Framework的所有其他功能整合在一起。

***spring-webmvc-portlet*** 模块（也称为Web-Portlet模块）提供了在Portlet环境中使用的MVC实现，并镜像了基于Servlet的spring-webmvc模块的功能。


### 测试
***spring-test*** 模块支持使用JUnit或TestNG对Spring组件进行单元测试和集成测试。它提供了Spring ApplicationContexts的一致加载和这些上下文的缓存。它还提供可用于隔离测试代码的模拟对象。

## 使用场景
***
前面描述的构建块使Spring在许多场景中可以合理选择，从在资源有限的设备上运行的嵌入式应用程序到使用Spring的事务管理功能和web框架集成的全面企业应用程序。

*典型的完整Spring Web应用程序*
![典型的完整Spring Web应用程序](/images/spring/overview-full.png.pagespeed.ce.sC26wirtWB.png)Spring的声明式事务管理功能使Web应用程序完全事务性，就像使用EJB容器管理的事务一样。所有您的自定义业务逻辑都可以使用简单的POJO实现，并由Spring的IoC容器进行管理。其他的服务包括对发送邮件的支持和独立于web层验证，可让您选择执行验证规则的位置。Spring的ORM支持是与JPA，Hibernate和JDO集成;比如，当你使用Hibernate，你可以继续使用已有的映射文件和标准的Hibernate SessionFactory配置。表单控制器将Web层与域模型无缝集成，从而无需ActionForms或将HTTP参数转换为你的域模型值的其他类。

*使用第三方web框架的Spring中间层*
![使用第三方web框架的Spring中间层](/images/spring/overview-thirdparty-web.png.pagespeed.ce.1lJso2G8WP.png)
有时一些情况不允许你完全切换到不同的框架。Spring框架并不强制您使用它的一切;它不是一个要么全部采用要么全不采用的解决方案。使用Struts，Tapestry，JSF或其他UI框架构建的现有前端可以与基于Spring的中间层集成，这允许您使用Spring事务功能。您只需要使用ApplicationContext连接您的业务逻辑，并使用WebApplicationContext来集成您的Web层。

*远程使用场景*
![远程使用场景](/images/spring/overview-remoting.png.pagespeed.ce.HIMsJb_Xya.png)
当您需要通过Web服务访问现有代码时，可以使用Spring的Hessian-，Burlap-，Rmi-或JaxRpcProxyFactory类。启用对现有应用程序的远程访问并不困难。

*EJBs - 包装现有的POJO*
![EJBs - 包装现有的POJO](/images/spring/overview-ejb.png.pagespeed.ce.VN88UiKUhA.png)
Spring Framework还为Enterprise JavaBeans提供了一个访问和抽象层，使您能够重用现有的POJO，并将其包装在无状态会话bean中，以便在可能需要声明式安全性的可扩展的，故障安全的Web应用程序中使用。

### 依赖管理和命名惯例
依赖管理和依赖注入是不同的事情。为了将Spring的这些不错的功能（如依赖注入）引入到应用程序中，你需要组合所有必须的库（jar文件），并且在运行时期将它们导入你的类路径中，也可能在编译时期。这些依赖不是注入的虚拟组件，而是文件系统中的物理资源（通常为）。依赖管理的过程包括定位这些资源，存储并添加它们到类路径。依赖可以是直接依赖（如我的程序在运行时期依赖Spring），或简介依赖（如我的程序依赖commons-dbcp，而它又依赖commons-pool）。间接依赖关系也被称为“传递性”，它们是最难识别和管理的依赖关系。

如果你要使用Spring，你需要获得一个包含你所需要的Spring部分的jar库的副本。为了使这更容易，Spring被打包成一系列的模块，这样可以尽可能地分离依赖关系，因此，例如如果您不想编写Web应用程序，则不需要spring-web模块。要在本指南中引用Spring库模块，我们使用一个简写命名约定spring-\*或spring-\*.jar，这里\*代表模块的简称（如spring-core, spring-webmvc, spring-jms，等等）。而实际你使用的jar文件名称通常是模块名后面连接着版本号（比如spring-core-4.3.10.RELEASE.jar）。

Spring Framework的每个版本都会将artifacts发布到以下位置：
- Maven Central，这是Maven查询的默认仓库，并且不需要任何特殊配置来使用。Spring的许多常见的库也可以从Maven Central获得，Spring社区的大部分使用Maven进行依赖关系管理，所以这对他们来说很方便。这里jar的名称是 spring-\*-&lt;version>.jar的形式，Maven groupId是*org.springframework*.
- 在专门用于Spring的公共Maven仓库。除了最终的GA版本，这个仓库还托管这开发快照版和里程碑版。jar文件名称与Maven Central中使用同样的形式，所以这是一个有用的地方，可以让开发版本的Spring与在Maven Central中部署的其他库一起使用。该存储库还包含捆绑分发zip文件，其中包含所有Spring jar，捆绑在一起以便于下载。

所以你需要决定的第一件事是如何管理你的依赖：我们通常建议使用像Maven，Gradle或Ivy这样的自动化系统，但您也可以通过自己下载所有的jar来手动进行操作。

下面你将会看到Spring工件的列表。有关每个模块的更完整的描述，请参见第2.2节“[框架模块](#框架模块)”。

|GroupId|ArtifactId|描述|
|-------|----------|----|
|org.springframework|spring-aop|基于代理的AOP支持|
|org.springframework|spring-aspects|基于AspectJ的切面|
|org.springframework|spring-beans|Beans支持, 包括Groovy
|org.springframework|spring-context|运行时期应用程序上下文，包括调度和远程抽象|
|org.springframework|spring-context-support|支持类将常见的第三方库集成到Spring应用程序上下文中|
|org.springframework|spring-core|核心实用程序，被许多其他Spring模块使用|
|org.springframework|spring-expression|Spring表达式语言 (SpEL)|
|org.springframework|spring-instrument|用于JVM引导的Instrumentation代理|
|org.springframework|spring-instrument-tomcat|Tomcat的Instrumentation代理|
|org.springframework|spring-jdbc|JDBC支持包，包括DataSource设置和JDBC访问支持|
|org.springframework|spring-jms|JMS支持包，包括发送/接收JMS消息的帮助类|
|org.springframework|spring-messaging|支持消息架构和协议|
|org.springframework|spring-orm|对象/关系映射，包括JPA和Hibernate支持|
|org.springframework|spring-oxm|对象/ XML映射|
|org.springframework|spring-test|支持单元测试和集成测试Spring组件|
|org.springframework|spring-tx|事务基础框架，包括DAO支持和JCA整合|
|org.springframework|spring-web|基础网络支持，包括Web客户端和基于Web的远程处理|
|org.springframework|spring-webmvc|基于HTTP的Model-View-Controller和REST端点，用于Servlet堆栈|
|org.springframework|spring-webmvc-portlet|在Portlet环境中使用MVC实现|
|org.springframework|spring-websocket|的WebSocket和SockJS基础框架，包括STOMP消息支持|