title: Spring 概述
id: 1500863364622
author: 不识
tags:
  - Spring
categories:
  - Spring
date: 2017-07-24 10:29:00
---
<style>
strong {
    margin: 2px;
    background-color: #f2f2f2;
    border: 1px solid #ccc;
    border-radius: 4px;
    padding: 1px 3px 0;
    text-shadow: none;
    white-space: nowrap;
	color: #6d180b;
	font-weight: normal;
}
</style>

[原文链接:](https://docs.spring.io/spring/docs/4.3.10.RELEASE/spring-framework-reference/htmlsingle/#spring-introduction) Spring Framework是一个轻量级解决方案，也是构建企业级应用程序的潜在一站式服务。但是，Spring是模块化的，允许你只使用你所需的部分，而无需引用其他的部分。你可以在任何web框架的顶层使用IOC容器，但也可以仅使用 Hibernate integration code或 JDBC abstraction layer。Spring框架支持声明式事务管理，通过RMI或web service远程访问你的业务逻辑，以及用于数据持久化的各种选项。它提供了一个全功能的MVC框架，使你能够将AOP透明地集成到你的软件中。

Spring被设计为非侵入式的，这意味着你的域逻辑代码通常不需要依赖框架本身。在你的集成层（比如数据访问层）中，将存在对数据访问技术和Spring库的一些依赖。但是，应该很容易将这些依赖关系与你的代码库的其余部分隔离开来。
<!-- more -->
***
# Spring入门
***
本参考指南提供有关Spring框架的详细信息。它为所有功能提供了全面的文档，以及Spring的一些基本概念（如“依赖注入”）的一些背景知识。

如果你刚开始使用Spring，则可能需要通过创建基于Spring Boot的应用程序来开始使用Spring Framework。Spring Boot提供一种创建一个基于Spring生成就绪应用程序的快捷（主观上）方式。它基于Spring框架，有利于配置的约定，并且旨在尽可能快地开始运行。

你可以使用[start.spring.io](http://start.spring.io/)生成一个基本项目，或按照“[入门指南](https://spring.io/guides)”中的一个指导，如“[开始构建RESTful Web服务](https://spring.io/guides/gs/rest-service/)”。除了易于学习以外，这些指南非常重视任务，它们大部分都是基于Spring Boot。它们还包含了当解决特定问题时你可能想要考虑的Spring解决方案中的其他项目。
***
# Spring框架简介
***
Spring Framework是一个Java平台，为开发Java应用程序提供全面的基础框架支持。Spring处理这些基础框架，以便你可以专注于你的应用程序。

Spring使你能够从“plain old Java objects”（POJO）构建应用程序，并将企业服务非侵入式应用于POJO。此功能适用于全部的Java SE编程模型以及部分Java EE。

作为应用程序开发人员，你可以从Spring平台中获益：
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
"问题是，哪一方面的控制（他们）反转了？"Martin Fowler在2004年在他的网站上提出了关于控制反转（IoC）的问题.Fowler建议重新命名这个原理，使其更加自明，并提出了*依赖注入*这个名称。

## 框架模块
***
Spring框架由被组织成约20个模块的功能组成。这些模块被分组为核心容器（Core Container），数据访问/集成（Data Access/Integration），Web，AOP（面向切面编程），Instrumentation，消息和测试，如下表所示。
![Spring框架概览](/images/spring/spring-overview.png.pagespeed.ce.XVe1noRCMt.png)
以下部分列出了每个功能的可用模块和它的artifact名称及其涵盖的主题。Artifact名称与依赖关系管理工具中使用的*artifact ID*相关。

### 核心容器
核心容器由***spring-core***，***spring-beans***，***spring-context***，***spring-context-support*** 和***spring-expression***（Spring表达式语言）模块组成。

***spring-core*** 和***spring-beans*** 模块提供框架的基本部分，包括IoC和依赖注入功能。***BeanFactory*** 是工厂模式的一个复杂实现。它消除了对单例编程的需要，并允许你将依赖关系的配置和说明与实际程序逻辑分离。

Context(***spring-context*** )模块在Core and Beans模块提供的实体基础上构建：它是以框架式方式访问对象的一种手段，这类似于JNDI注册。Context模块从Beans模块继承其功能，并且添加了对国际化的支持（例如使用资源束），事件传播，资源加载以及通过例如Servlet的容器透明地创建上下文。Context模块还支持Java EE功能，如EJB，JMX和基本远程处理。***ApplicationContext*** 接口是Context模块的焦点。***spring-context-support*** 提供了将常见的第三方库集成到Spring应用程序上下文中的支持，用于缓存（EhCache, Guava, JCache），邮件（JavaMail），作业调度（CommonJ, Quartz）以及模板引擎（FreeMarker, JasperReports, Velocity）。

***spring-expression*** 模块提供了强大的表达式语言，用于在运行时查询和操作对象图。它是JSP 2.1规范中规定的统一表达语言（统一EL）的扩展。该语言支持设置和获取属性值，属性分配，方法调用，访问数组，集合和索引器的内容，逻辑和算术运算符，命名变量以及从Spring的IoC容器中以名称检索对象。它还支持列表投影与选择以及常见列表聚合。

### AOP和Instrumentation

***spring-aop*** 模块提供了符合AOP联盟的面向切面的编程实现，允许你定义方法拦截器和切入点，来将应分离的功能代码完全解耦。使用源代码级元数据功能，你还可以以类似于.NET属性的方式将行为信息合并到代码中。

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

***典型的完整Spring Web应用程序***
![典型的完整Spring Web应用程序](/images/spring/overview-full.png.pagespeed.ce.sC26wirtWB.png)Spring的声明式事务管理功能使Web应用程序完全事务性，就像使用EJB容器管理的事务一样。所有你的自定义业务逻辑都可以使用简单的POJO实现，并由Spring的IoC容器进行管理。其他的服务包括对发送邮件的支持和独立于web层验证，可让你选择执行验证规则的位置。Spring的ORM支持是与JPA，Hibernate和JDO集成;比如，当你使用Hibernate，你可以继续使用已有的映射文件和标准的Hibernate SessionFactory配置。表单控制器将Web层与域模型无缝集成，从而无需ActionForms或将HTTP参数转换为你的域模型值的其他类。

***使用第三方web框架的Spring中间层***
![使用第三方web框架的Spring中间层](/images/spring/overview-thirdparty-web.png.pagespeed.ce.1lJso2G8WP.png)
有时一些情况不允许你完全切换到不同的框架。Spring框架并不强制你使用它的一切;它不是一个要么全部采用要么全不采用的解决方案。使用Struts，Tapestry，JSF或其他UI框架构建的现有前端可以与基于Spring的中间层集成，这允许你使用Spring事务功能。你只需要使用ApplicationContext连接你的业务逻辑，并使用WebApplicationContext来集成你的Web层。

***远程使用场景***
![远程使用场景](/images/spring/overview-remoting.png.pagespeed.ce.HIMsJb_Xya.png)
当你需要通过Web服务访问现有代码时，可以使用Spring的Hessian-，Burlap-，Rmi-或JaxRpcProxyFactory类。启用对现有应用程序的远程访问并不困难。

***EJBs - 包装现有的POJO***
![EJBs - 包装现有的POJO](/images/spring/overview-ejb.png.pagespeed.ce.VN88UiKUhA.png)
Spring Framework还为Enterprise JavaBeans提供了一个访问和抽象层，使你能够重用现有的POJO，并将其包装在无状态会话bean中，以便在可能需要声明式安全性的可扩展的，故障安全的Web应用程序中使用。

### 依赖管理和命名惯例
依赖管理和依赖注入是不同的事情。为了将Spring的这些不错的功能（如依赖注入）引入到应用程序中，你需要组合所有必须的库（jar文件），并且在运行时期或编译时期将它们导入你的类路径中。这些依赖不是注入的虚拟组件，而是文件系统中的物理资源（通常为）。依赖管理的过程包括定位这些资源，存储并添加它们到类路径。依赖可以是直接依赖（如我的程序在运行时期依赖Spring），或间接依赖（如我的程序依赖commons-dbcp，而它又依赖commons-pool）。间接依赖关系也被称为“传递性”，它们是最难识别和管理的依赖关系。

如果你要使用Spring，你需要获得一个包含你所需要的Spring部分的jar库的副本。为了使这更容易，Spring被打包成一系列的模块，这样可以尽可能地分离依赖关系，因此，例如如果你不想编写Web应用程序，则不需要spring-web模块。要在本指南中引用Spring库模块，我们使用一个简写命名约定spring-\*或spring-\*.jar，这里\*代表模块的简称（如spring-core, spring-webmvc, spring-jms，等等）。而实际你使用的jar文件名称通常是模块名后面连接着版本号（比如spring-core-4.3.10.RELEASE.jar）。

Spring Framework的每个版本都会将artifacts发布到以下位置：
- Maven Central，这是Maven查询的默认仓库，并且不需要任何特殊配置来使用。Spring的许多常见的库也可以从Maven Central获得，Spring社区的大部分使用Maven进行依赖关系管理，所以这对他们来说很方便。这里jar的名称是 spring - \* - &lt;version>.jar的形式，Maven groupId是*org.springframework*.
- 在专门用于Spring的公共Maven仓库。除了最终的GA版本，这个仓库还托管这开发快照版和里程碑版。jar文件名称与Maven Central中使用同样的形式，所以这是一个对开发者有用的地方，可以让开发版本的Spring与在Maven Central中部署的其他库一起使用。该存储库还包含捆绑分发zip文件，其中包含所有Spring jar，捆绑在一起以便于下载。

所以你需要决定的第一件事是如何管理你的依赖：我们通常建议使用像Maven，Gradle或Ivy这样的自动化系统，但你也可以通过自己下载所有的jar来手动进行操作。

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

#### Spring依赖和依赖Spring   
虽然Spring为大型企业和其他外部工具提供集成和支持，它有意将它的强制依赖保持在最小：你不必为了简单的案例使用Spring而去定位和下载（甚至自动）大量的jar库。对于基本依赖注入，只有一个强制性的外部依赖关系，也就是用于日志记录（有关日志记录选项的更详细描述，请参阅下文）。

接下来我们概述需要配置一个依赖Spring的应用程序，首先是使用Maven，然后是Gradle，最后是Ivy。在这些所有情况下，如果哪些不清楚，请参阅你的依赖管理系统的文档，或者看些简单代码-Spring它自己在构建的时候使用Gradle来管理依赖，并且我们的示例大多使用Gradle或Maven。

#### Maven依赖管理
如果你使用Maven作为依赖管理，那么你甚至不必明确的提供日志依赖。比如，要创建应用程序上下文并使用依赖注入来配置应用程序，你的Maven依赖项将如下所示：
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>4.3.10.RELEASE</version>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```
就是这样。注意，如果你不需要针对Spring APIs进行编译，那么scope可以被声明为runtime，通常情况下这是基本依赖注入用例的情况。

上面的例子适用于Maven中央仓库。要想使用Spring的Maven仓库（如为了使用里程碑和开发快照版），你需要在你的Maven配置中指定仓库的位置。对于全部版本：
```xml
<repositories>
    <repository>
        <id>io.spring.repo.maven.release</id>
        <url>http://repo.spring.io/release/</url>
        <snapshots><enabled>false</enabled></snapshots>
    </repository>
</repositories>
```
对于里程碑版：
```xml
<repositories>
    <repository>
        <id>io.spring.repo.maven.milestone</id>
        <url>http://repo.spring.io/milestone/</url>
        <snapshots><enabled>false</enabled></snapshots>
    </repository>
</repositories>
```
对于快照版：
```xml
<repositories>
    <repository>
        <id>io.spring.repo.maven.snapshot</id>
        <url>http://repo.spring.io/snapshot/</url>
        <snapshots><enabled>true</enabled></snapshots>
    </repository>
</repositories>
```

#### Maven“物料清单”依赖**
使用Maven时，可能会意外混合不同版本的Spring JAR。比如，你可能发现一个第三方库或者另一个Spring项目，将传递依赖传递给了旧的版本。如果你忘记自己明确声明一个直接依赖，可能会出现各种意外问题。

为了克服这些问题，Maven支持“物料单”（BOM）依赖的概念。你可以在dependencyManagement部分中导入spring-framework-bom，以确保所有的spring 依赖（直接和传递）都是相同的版本。
```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-framework-bom</artifactId>
            <version>4.3.10.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```
使用BOM的另外一个好处是，你不再需要在依赖于Spring框架的artifact时指定&lt;version>属性：
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
    </dependency>
<dependencies>
```

#### Gradle依赖管理
要使用具有Gradle构建系统的Spring存储库，请在存储库部分中包含适当的URL：
```
repositories {
    mavenCentral()
    // and optionally...
    maven { url "http://repo.spring.io/release" }
}
```
你可以根据需要将存储库URL从/ release更改为/ milestone或/ snapshot。一旦存储库被配置，你可以通常使用Gradle方式来声明依赖：
```
dependencies {
    compile("org.springframework:spring-context:4.3.10.RELEASE")
    testCompile("org.springframework:spring-test:4.3.10.RELEASE")
}
```
#### Ivy依赖管理
如果你喜欢使用Ivy来管理依赖关系，那么还有类似的配置选项。

要配置Ivy指向Spring存储库，请将以下解析器添加到你的ivysettings.xml中：
```xml
<resolvers>
    <ibiblio name="io.spring.repo.maven.release"
            m2compatible="true"
            root="http://repo.spring.io/release/"/>
</resolvers>
```
你可以根据需要将根URL从/ release /更改为/ milestone /或/ snapshot /。 配置完成后，你可以按通常的方式添加依赖项。例如（在ivy.xml中）：
```xml
<dependency org="org.springframework"
    name="spring-core" rev="4.3.10.RELEASE" conf="compile->runtime"/>
```
#### 分发Zip文件
虽然使用支持依赖关系管理的构建系统是推荐的获取Spring框架的方法，但仍然可以下载分发zip文件。

分发zip被发布到Spring Maven仓库中（这只是为了我们的方便，你不需要Maven或任何其他构建系统来下载它们）。

要下载分发zip打开Web浏览器到http://repo.spring.io/release/org/springframework/spring ，并为所需的版本选择适当的子文件夹。分发文件以-dist.zip结尾，例如spring-framework- {spring-version} -RELEASE-dist.zip。发行版还会发布里程碑和快照。

### 日志
对于Spring来说，日志记录是非常重要的依赖因为：
- 它是唯一的强制依赖
- 每个人都喜欢从他们使用的工具中看到一些输出
- Spring与许多其他工具集成，这些工具都可以选择日志记录依赖关系。

应用程序开发人员的目标之一通常是将统一的日志记录配置在整个应用程序的中央位置，包括所有外部组件。自从有了这么多的日志框架可以选择，这项工作比以前困难很多。

Spring中的强制性日志依赖是Jakarta Commons Logging API（JCL）。我们针对JCL进行编译，并且我们还使JCL的Log对象对所有继承自Spring框架的类可见。对用户来说，所有版本的Spring都使用相同的日志库很重要：这样迁移很容易，因为即使拓展自Spring的应用程序仍然保留向后兼容性。我们这样做的方式是使Spring中的一个模块明确地依赖于commons-logging（JCL的规范实现），然后在编译时使所有其他模块依赖于它。例如，如果你使用Maven，并且想知道在哪里可以获取对commons-logging的依赖，那么它来自Spring，明确来说是来自名为spring-core的中央模块。

commons-logging的好处在于，你不需要任何其他操作来使你的应用程序正常工作。它有一个运行时期发现算法，可以在classpath中的众所周知的地方查找其他的日志框架，并且使用一个它认为合适的使用（或者你可以可以告诉它你需要哪一个）。如果没有其他可用的，你可以从JDK（java.util.logging或者简称JUL）中看到非常漂亮的日志。你应该发现，在大多数情况下，你的Spring应用程序可以快乐地登录到控制台，这很重要。

#### 使用Log4j 1.2或2.X
> Log4j 1.2在此期间已经终止。此外，Log4j 2.3是最新的Java 6兼容版本，较新的Log4j 2.x版本需要Java 7+版本。

为了配置和管理的目的，许多人使用Log4j作为日志框架。它是高效和成熟的，实际上它是我们在构建Spring时在运行时使用的。Spring还提供了一些用于配置和初始化Log4j的工具，所以它在一些模块中它有一个对Log4j的可选的编译时期依赖。

要使Log4j 1.2与默认的JCL依赖（commons-logging）一起使用，所有你需要做的是将Log4j放入到classpath下，并且提供给它一个配置文件（log4j.properties或log4j.xml在classpath根下）。所以对于Maven用户来说，这是你的依赖声明：

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>4.3.10.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
</dependencies>
```
这还有一个简单的用于记录日志到控制台的log4j.properties：
```
log4j.rootCategory=INFO, stdout

log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{ABSOLUTE} %5p %t %c{2}:%L - %m%n

log4j.category.org.springframework.beans.factory=DEBUG
```
要和JCL一起私用Log4j 2.x，所有你需要做的是将Log4j放入到类路径，并且提供给它一个配置文件（log4j2.xml, log4j2.properties,或者其他支持的配置格式）。对于Maven用户，需要的最小依赖是：
```xml
<dependencies>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-core</artifactId>
        <version>2.6.2</version>
    </dependency>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-jcl</artifactId>
        <version>2.6.2</version>
    </dependency>
</dependencies>
```
如果你还希望启用SLF4J委托给Log4j，例如对于默认使用SLF4J的其他库，还需要以下依赖关系：
```xml
<dependencies>
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>2.6.2</version>
  </dependency>
</dependencies>
```
这有一个记录日志到控制台的log4j2.xml示例：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
  <Appenders>
    <Console name="Console" target="SYSTEM_OUT">
      <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
  </Appenders>
  <Loggers>
    <Logger name="org.springframework.beans.factory" level="DEBUG"/>
    <Root level="error">
      <AppenderRef ref="Console"/>
    </Root>
  </Loggers>
</Configuration>
```

#### 避免使用Commons Logging
不幸的是，在标准commons-logging API中的运行时期发现算法，方便终端用户的同时，也会带来问题。如果你想避免JCL的标准查找，基本上有两种方法来关闭它：
1. 从spring-core模块中排除依赖（因为它是明确依赖于commons-logging的唯一模块）
2. 依赖一个特殊的commons-logging依赖，用一个空的jar取代这个库（更多细节可以在[SLF4J FAQ](http://slf4j.org/faq.html#excludingJCL)中找到）

要想排除commos-logging，在你的**dependencyManagement**部分添加一下内容：
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>4.3.10.RELEASE</version>
        <exclusions>
            <exclusion>
                <groupId>commons-logging</groupId>
                <artifactId>commons-logging</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
</dependencies>
```
现在这个应用程序目前被打破了，因为在类路径上没有JCL API实现，所以要解决它，必须提供一个新的。在下一节，我们向你展示如何使用SLF4J提供一个JCL的替代实现。

#### 使用SLF4J与Log4j或Logback
Simple Logging Facade for Java (SLF4J) 是Spring常用的其他库使用的流行API。它通常和Logback一起使用，Logback是SLF4J API的本地实现。

SLF4J提供绑定到许多常见的日志记录框架，包括Log4j，反过来也是在其他日志框架和它自身之间桥接。所以要在Spring使用SLF4J，你需要使用SLF4J-JCL桥接来替换commos-logging依赖。一旦你这么做，那么来自Spring内的日志记录调用将会被转换为SLF4J API的日志调用，所以如果在你的应用程序中的其他库使用这个API，那么你有一个地方来配置和管理日志记录。

一个常见的选择可能是将Spring桥接到SLF4J，然后提供一个从SLF4J到Log4j的显示绑定。你需要提供几个依赖（并排除现有的commons-logging）：JCL桥，绑定到Log4j的SLF4j，和Log4j本身。在Maven你会这样做。
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>4.3.10.RELEASE</version>
        <exclusions>
            <exclusion>
                <groupId>commons-logging</groupId>
                <artifactId>commons-logging</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>jcl-over-slf4j</artifactId>
        <version>1.7.21</version>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>1.7.21</version>
    </dependency>
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
</dependencies>
```
在SLF4J用户中一个更为常见的选择是，直接绑定到Logback，这使用了更少的步骤，生成更少的依赖。这样消除了额外的绑定步骤，因为Logback直接实现了SLF4J，所以你只需要依赖两个库，名字为jcl-over-slf4j和logback。
```xml
<dependencies>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>jcl-over-slf4j</artifactId>
        <version>1.7.21</version>
    </dependency>
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.1.7</version>
    </dependency>
</dependencies>
```

#### 使用JUL（java.util.logging）
默认情况下Commons Logging会被委托给java.util.logging,前提是在类路径中没有检测到Log4j。所以这里没有特别的依赖需要设置：只需在独立应用程序（具有JDK级别的自定义或默认JUL设置）或应用程序服务器的日志系统（以及其全系统的JUL设置）中使用不带有外部依赖关系的日志输出到java.util.logging ）。

#### Commons Logging在WebSphere上
Spring应用程序可以在本身提供JCL实现的容器上运行，例如IBM的WebSphere Application Server（WAS）。这本身并不引起问题，但是它导致有两个不同的场景需要理解：

在“parent first”ClassLoader委托模型（WAS中的默认值）中，应用程序将始终拾取服务器提供的Commons Logging版本，委托给WAS记录子系统（实际上基于JUL）。JCL的应用程序提供的变体，无论是标准的Commons Logging还是JCL-over-SLF4J桥，实际都将被忽略，以及任何本地包含的日志提供程序。

在你的应用程序中，使用“parent last”委托模式（这是常规Servlet容器中的默认值，但在WAS上是一个需要明确配置的选项），应用程序提供的Commons Logging变体会被采用，使你能够设置本地包含的日志提供程序，例如Log4j或Logback。在没有本地日志提供程序的情况下，常规Commons Logging将默认委托给JUL，有效地记录到WebSphere的日志记录子系统，像在“parent first”场景中。

总而言之，我们建议将“Spring”应用程序部署在“parent last”模型中，因为它实际地允许本地提供程序以及服务器的日志子系统。