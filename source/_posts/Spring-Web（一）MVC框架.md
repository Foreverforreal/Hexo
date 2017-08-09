title: Spring Web（一）MVC框架
id: 1501315692211
author: 不识
tags:
  - Spring
categories:
  - Spring
date: 2017-07-29 16:08:00
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

[原文链接](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#mvc)：参考文档的这一部分涵盖了Spring框架对表示层（特别是基于Web的演示层）的支持，包括在Web应用程序中支持WebSocket风格的消息传递。

Spring Framework自己的Web框架，Spring Web MVC，在前两章中有介绍。随后的章节涉及Spring框架与其他Web技术（如JSF）的集成。

接下来是Spring Framework的MVC portlet框架。
<!-- more -->

***
# Spring Web MVC框架简介
***
Spring Web模型-视图-控制器（MVC）框架是围绕一个**DispatcherServlet**设计，它将请求分派给处理器（handler），Spring MVC具有可配置的处理器映射器，视图解析器，本地化，时区和主题解析以及上传文件支持。默认的handler是基于**@Controller**和**@RequestMapping**注解，提供了广泛的灵活处理方法。随着Spring 3.0的引入，**@Controller**机制还允许你通过**@PathVariable**注解和其他功能创建RESTful Web站点和应用程序。

<div style="border: 1px solid #ccc;background-color:#f8f8f8;padding:20px;">“开放扩展..."在Spring Web MVC和Spring中的一个关键设计原则是“开放扩展，关闭修改"原则。<br/>
Spring Web MVC的核心类中的一些方法被标记为final。作为开发人员，您不能覆盖这些方法来提供自己的行为。这并不是随意设计的，而是特别考虑到这个原则。<br/>
有关这个原理的解释，请参考Seth Ladd的Expert Spring Web MVC和Web Flow;具体参见第一版第117页的“A Look At Design"一节。或者，参见
[Bob Martin, The Open-Closed Principle (PDF)](https://www.cs.duke.edu/courses/fall07/cps108/papers/ocp.pdf)
当您使用Spring MVC时，您不能向final方法添加advice 。例如，您不能向AbstractController.setSynchronizeOnSession()方法添加advice 。有关AOP代理的更多信息，以及为什么不能向final方法添加advice ，请参阅第11.6.1节“[了解AOP代理"](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#aop-understanding-aop-proxies)。
</div> 
在Spring Web MVC中，你可以使用任何对象作为命令或表单支持对象;您不需要实现框架特定的接口或基类。Spring的数据绑定是高度灵活的：例如，它将类型不匹配视为可由应用程序评估的验证错误，而不是系统错误。因此，你不需要将业务对象的属性复制为表单对象中简单、无类型的字符串，仅用于处理无效提交，或者用于正确转换字符串。相反，它通常最好直接绑定到您的业务对象。

Spring的视图解析非常灵活。**Controller**通常负责准备一个带有数据和选择的视图名称的模型Map，但它也可以直接写入响应流并完成请求。视图名称解析是高度可配置的，通过文件拓展或Accept header content type negotiation，通过bean名称，一个属性文件，或甚至一个ViewResolver实现。这个model（MVC中的M）是一个Map接口，它允许对视图技术的完全抽象。你可以直接与基于模板的渲染技术（如JSP，Velocity和Freemarker）集成，或直接生成XML，JSON，Atom和许多其他类型的内容。model Map简单地被转换成适当的格式，如JSP请求属性，Velocity模板模型。      

## Spring Web MVC的特点
***

<div style="border: 1px solid #ccc;background-color:#f8f8f8;padding:20px;">Spring Web Flow
Spring Web Flow（SWF）旨在成为管理Web应用程序页面流的最佳解决方案。<br/>
在Servlet和Portlet环境中，SWF与Spring MVC和JSF等现有框架集成。如果您有一个业务流程会受益于会话模型而不是纯粹的请求模型，那么SWF可能是这个的解决方案。<br/>
SWF允许你捕获逻辑页面流作为自包含模块，以便可以在不同场景中重用，因此非常适合通过controller导航来引导用户驱动业务流程的Web应用程序模块。
</div>
Spring的Web模块包含许多独特的Web支持功能：
- *清晰的角色分离*。每个角色 — 控制器，验证器，命令对象，表单对象，模型对象，**DispatcherServlet**，处理器映射器，视图解析器等等 - 可以由专门的对象来实现。
- *框架以及作为JavaBeans的应用程序类的强大而直观的配置。*此配置功能包括跨上下文的简单引用，例如从Web控制器到业务对象和验证器。
- *适应性，非侵入性和灵活性。*定义任何你需要的控制器方法签名，可能使用给定方案的参数注解之一（例如@RequestParam，@RequestHeader，@PathVariable等等）。
- *可重用的业务代码，不需要复制。*使用已有的业务对象作为命令或表单对象，而不是镜像它们来继承特定的框架基类。
- *可定制的绑定和验证。*类型不匹配作为保持违规值的应用程序级验证错误，本地化日期和数字绑定等，而不是对只使用仅包含字符串的表单对象进行手动解析和转换为业务对象。
- *可定制的处理器映射和视图解析。*处理器映射和视图解析策略的范围从简单的基于URL的配置到复杂的专用解析策略。Spring比那些需要特定技术的Web MVC框架更加灵活。
- *灵活的model传递。*使用name/value Map的model传递支持与任何视图技术的轻松集成。
- *可定制的区域设置，时区和主题解析，支持带有或不带有Spring标签库的JSP，支持JSTL，支持Velocity，无需额外的桥接等。*
- *一个简单而强大的JSP标签库，被称为Spring标签库，为数据绑定和主题等功能提供支持。*自定义标签在标记代码方面允许具有最大的灵活性。有关标签库描述符的信息，请参见附录标题为[第43章，spring JSP标签库](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#spring-tld)
- *在Spring 2.0中引入的JSP表单标签库，使得在JSP页面中的写入表单更容易。*有关标签库描述符的信息，请参见附录标题为[Chapter 44，spring-form JSP Tag Library](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#spring-form-tld)
- *Bean的生命周期作用域在当前的HTTP请求或HTTP Session中* 这不是Spring MVC本身的一个特定功能，而是Spring MVC使用的WebApplicationContext容器。这些bean域在第7.5.4节[“Request, session, global session, application和 WebSocket域"](/2017/07/25/Spring-核心技术（一）IoC容器/#Request, session, global session, application和 WebSocket域
)中描述

## 其他MVC实现的可插拔性
***
对于某些项目，非Spring MVC实现更为可取。许多团队希望利用他们在现有的技能和工具上的投资，例如使用JSF。

如果你不想使用Spring的Web MVC，但打算利用Spring提供的其他解决方案，你可以轻松地将Web MVC框架与你选择的Spring集成。通过它的**ContextLoaderListener**简单地启动一个Spring根应用程序上下文，并通过任何action对象中的ServletContext属性（或Spring的各自的帮助方法）访问它。没有涉及“插件"，因此不需要专门的集成。从Web层的角度来看，你只是将Spring作为库使用，将根应用程序上下文实例作为入口点。

即使不使用Spring Web MVC,注册bean和Spring的Service也是唾手可得的。在这种情况下，Spring不会与其他Web框架竞争。它只是解决了纯Web MVC框架没有解决的许多事情，从bean配置到数据访问和事务处理。所以你可以使用Spring中间层和/或数据访问层来丰富您的应用程序，即使你只想使用JDBC或Hibernate的事务抽象。

***
# DispatcherServlet
***
Spring的Web MVC框架与许多其他Web MVC框架一样，请求驱动，围绕一个中央Servlet设计，将请求分发给控制器，并提供了其他促进Web应用程序开发的功能。然而，Spring的**DispatcherServlet**做的不仅仅是这些。它完全与Srping Ioc容器集成，这样允许你使用Spring有的其他任何功能。

Spring Web MVC **DispatcherServlet**的请求处理工作流程如下图所示。精通模式的读者会意识到DispatcherServlet是“前端控制器"设计模式的表现（这是Spring Web MVC与许多其他领先的Web框架共享的一种模式）。   

*Spring Web MVC（高级别）中的请求处理工作流程*
![mvc](/images/spring/mvc.png)

**DispatcherServlet**实际上是一个Servlet（它继承自HttpServlet基类），并因此在你的web应用程序中声明。你需要通过使用URL映射，来映射你想要**DispatcherServlet**处理的请求。这有一个在Serlvet 3.0+ 环境中标准的Java EE Servlet配置。
```java
public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext container) {
        ServletRegistration.Dynamic registration = container.addServlet("example", new DispatcherServlet());
        registration.setLoadOnStartup(1);
        registration.addMapping("/example/*");
    }

}
```
在上面的例子中，所有以/example开示的请求都会由名为example的DispatcherServlet实例处理。

**WebApplicationInitializer**是一个由Spring MVC提供的接口，它确保你的基于代码的配置被检测到，并且自动用于初始化任何Servlet 3 容器。一个这个接口的抽象基类实现名称叫**AbstractAnnotationConfigDispatcherServletInitializer**，它通过简单地指定其servlet映射和列出配置类来更容易地注册**DispatcherServlet**-它是设置你的Spring MVC应用程序最推荐的方式。更多详细信息，请参阅[“基于代码的Servlet容器初始化"](#基于代码的Servlet容器初始化)。

**DispatcherServlet**实际上是一个**Servlet**（它继承自HttpServlet基类），并因此在你的web应用程序的**web.xml**中声明。你需要在相同的**web.xml**文件中通过使用URL映射，来映射你想要**DispatcherServlet**处理的请求。这是标准的Java EE Servlet配置。以下示例显示了这样的**DispatcherServlet**的声明和映射：

以下是等同于上面基于代码的配置的web.xml：
```xml
<web-app>
    <servlet>
        <servlet-name>example</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>example</servlet-name>
        <url-pattern>/example/*</url-pattern>
    </servlet-mapping>

</web-app>
```
如第7.15节[“ApplicationContext的附加功能"](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#context-introduction)中所述，Spring中的**ApplicationContext**实例可以被限定作用域。在Web MVC框架中，每个**DispatcherServlet**都有它自己的**WebApplicationContext**，这个上下文继承了在根**WebApplicationContext**中定义的所有bean。根WebApplicationContext应该包含应该在其他上下文和Servlet实例之间共享的所有的基础框架bean。这些继承的bean可以在特定servlet域内被覆盖，并且你可以在给定的Servlet实例本地定义一个新的特定域bean。

*Spring Web MVC中的典型上下文层次结构*
![mvc](/images/spring/mvc-context-hierarchy.png)

在初始化**DispatcherServlet**时，Spring MVC将在Web应用程序的**WEB-INF**目录中查找名为[servlet-name] -servlet.xml的文件，并创建在那里定义的bean，覆盖在全局域内任何使用相同名称定义的bean的定义。

请考虑以下DispatcherServlet Servlet配置（在web.xml文件中）：
```xml
<web-app>
    <servlet>
        <servlet-name>golfing</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>golfing</servlet-name>
        <url-pattern>/golfing/*</url-pattern>
    </servlet-mapping>
</web-app>
```
随着上面的Servlet配置到位，你会需要在你的应用程序中有一个名为**/WEB-INF/golfing-servlet.xml**的文件；这个文件将包含你的所有Spring Web MVC特定组件（beans）。你可以通过Servlet初始化参数更改此配置文件的确切位置（有关详细信息，请参阅下文）。

对于单DispatcherServlet情景来说，也可以只有一个root context。。

*Spring Web MVC中的单个根上下文*
![mvc](/images/spring/mvc-root-context.png)
这可以通过设置一个空的ContextConfigLocation servlet 初始化参数进行配置，如下所示：
```xml
<web-app>
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/root-context.xml</param-value>
    </context-param>
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value></param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/*</url-pattern>
    </servlet-mapping>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
</web-app>
```
**WebApplicationContext**是普通ApplicationContext的扩展，它有一些Web应用程序所需的额外功能。它与普通的ApplicationContext不同之处在于它能够解析主题（参见[第22.9节“使用主题"](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#mvc-themeresolver)），并且知道它与哪个Servlet相关联（通过链接到ServletContext）。**WebApplicationContext**绑定在**ServletContext**中，并且如果你需要访问它，可以通过在**RequestContextUtils**类上使用静态方法，随时查找**WebApplicationContext**。

请注意，我们可以通过基于Java的配置实现相同的配置：
```java
public class GolfingWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        // GolfingAppConfig定义将在root-context.xml中的bean
        return new Class[] { GolfingAppConfig.class };
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        // GolfingWebConfig定义了将在golfing-servlet.xml中的bean
        return new Class[] { GolfingWebConfig.class };
    }

    @Override
    protected String[] getServletMappings() {
        return new String[] { "/golfing/*" };
    }

}
```
## WebApplicationContext中的特殊Bean类型
***
Spring **DispatcherServlet**使用特殊的bean来处理请求并渲染适当的视图。这些bean是Spring MVC的一部分。您可以通过在**WebApplicationContext**中简单配置一个或多个选择要使用的特殊bean。但是，你最初不需要这样做，因为如果你没有做任何配置的话，Spring MVC已经维护了一个默认bean列表，。更多信息在下一节。首先看下表列出**DispatcherServlet**依赖的特殊bean类型。

*WebApplicationContext中的特殊bean类型*

|Bean类型|说明|
|--------|----|
|HandlerMapping|根据一些标准将传入的请求映射到处理器以及一个pre-和post-processors（处理器拦截器）列表上，其细节因**HandlerMapping**实现而异。最流行的实现支持注解控制器，但其他实现也存在。|
|HandlerAdapter|帮助**DispatcherServlet**调用映射到请求上的处理器，而不管实际调用哪个处理器。例如，调用一个注解的控制器需要解析各种注解。因此，**HandlerAdapter**的主要目的是将**DispatcherServlet**和这些细节屏蔽开。|
|HandlerExceptionResolver|映射异常到视图上，也允许更复杂的异常处理代码。|
|ViewResolver|解析基于逻辑字符串的视图名称为实际的**View**类型。|
|LocaleResolver&LocaleContextResolver|解析客户端使用的区域设置以及可能的时区，以便能够提供国际化的视图|
|ThemeResolver|解析你的Web应用程序可以使用的主题，例如，提供个性化的布局|
|MultipartResolver|解析multi-part请求，例如支持处理从HTML表单文件上传。|
|FlashMapManager|存储并获取“输入"和“输出"的FlashMap，它可以用于将属性从一个请求传递给另一个请求，通常是跨重定向。|

## 默认DispatcherServlet配置
***
如上一节中针对每个特殊bean所述，**DispatcherServlet**会维护默认使用的实现列表。这些信息保存在**org.springframework.web.servlet**包中的**DispatcherServlet.properties**文件中。

所有的特殊bean都有一些合理的默认值。虽然迟早你需要自定义一个或多个这些bean提供的属性。例如，配置一个**InternalResourceViewResolver**，设置他的**prefix**属性为视图文件的父路径是很常见的。

不管具体细节，需要明白的一个重要概念是，一旦你在你的**WebApplicationContext**中配置特殊bean（例如InternalResourceViewResolver ），你有效地覆盖了该特殊bean类型的默认实现列表。例如，如果配置了InternalResourceViewResolver，默认的ViewResolver实现列表会被忽视。

在第16节[“配置Spring MVC"](#配置Spring MVC)中，你将了解配置Spring MVC的其他选项，包括MVC Java配置和MVC XML命名空间，这两个都提供了一个简单的起点，这假定你对Spring MVC的工作原理并不太了解。无论你选择如何配置应用程序，本节中介绍的概念都是基本的，应该对你有所帮助。

## DispatcherServlet处理顺序
***
在你设置了一个**DispatcherServlet**，并且对于该特定**DispatcherServlet**进来了一个请求后，**DispatcherServlet**将按如下所示开始处理请求：
- 在这个请求中**WebApplicationContext**被搜索和绑定为一个属性，这样控制器和其他元素在处理过程中可以使用。默认它被绑定到**DispatcherServlet.WEB\_APPLICATION\_CONTEXT\_ATTRIBUTE**这个key上。
- 语言环境解析器被绑定到request上，这样使处理过程中的元素能够解析语言环境设置来在处理request时使用（渲染视图，准备数据等）。如果您不需要语言环境解析，则不需要它。
- 主题解析器被绑定到request上，使元素（如视图）决定要使用哪个主题。如果不使用主题，可以忽略它。
- 如果你指定了multiparts文件解析器，则会检查该request的multipart;如果找到multipart，request会被包装在一个**MultipartHttpServletRequest**中，以便处理过程中的其他元素进一步处理。有关multiparts处理的更多信息，请参见[第10节“Spring的multipart（文件上传）支持"](#Spring的multipart（文件上传）支持)。
- 搜索适当的处理器。如果找到一个处理器，与处理器关联的执行链（预处理器，后处理器和控制器）被执行，以便准备一个model或rendering。
- 如果返回一个modle，视图会被渲染。如果没有model返回，（可能是由于预处理器或后处理器基于安全原因拦截请求）则没有视图会被渲染，因为请求可能已经被完成了。

处理器异常解析器被声明在**WebApplicationContext**中，它可以提取在处理请求的过程中抛出的异常。使用这些异常解析器使你可以定义自定义的行为来解决异常。

Spring **DispatcherServlet**同样支持由Servlet API指定的*last-modification-date* 的返回。对于指定请求确定最后修改日期的过程很简单：**DispatcherServlet**查找一个适当的处理器映射器，并检验发现的处理器是否实现了**LastModified**接口。如果是的话，**LastModified**接口**long getLastModified(request)**方法的值被返回给客户端。

你可以通过向**web.xml**文件中的Servlet声明添加Servlet初始化参数（**init-param**元素）来自定义单独的**DispatcherServlet**实例。有关支持的参数列表，请参见下表。

*DispatcherServlet的初始化参数*

|参数|解释|
|----|----|
|contextClass|实现了**WebApplicationContext**的类，它实例化了这个Servlet使用的上下文。默认情况下，使用**XmlWebApplicationContext**。|
|contextConfigLocation|传递给上下文实例（由contextClass指定）的字符串，以指示那里可以找到上下文。该字符串可能包含多个字符串（使用逗号作为分隔符）来支持多个上下文。在多个上下文位置中定义了一个bean两次，那么最后位置定义的优先|
|namespace|**WebApplicationContext**的命名空间。默认为**[servlet-name] -servlet**。|

***
# 实现控制器
***
控制器提供对应用程序行为的访问，这些行为你通常通过一个service接口定义。控制器解释用户输入并将其转换为一个model，该model通过视图表现给用户。Spring以一个非常抽象的方式实现控制器，使您能够创建各种各样的控制器。

Spring 2.5为MVC控制器引入了基于注解的编程式模型，它使用诸如**@RequestMapping**，**@RequestParam**，**@ModelAttribute**等注解。此注解支持可用于Servlet MVC和Portlet MVC。以这种样式实现的控制器不必扩展特定的基类或实现特定的接口。此外，它们通常不直接依赖于Servlet或Portlet API，尽管您可以轻松配置对Servlet或Portlet设施的访问。

> 在Github上的可用的[spring-projects Org](https://github.com/spring-projects/)，许多Web应用程序利用本节中描述的注解支持，包括MvcShowcase，MvcAjax，MvcBasic，PetClinic，PetCare等。

```java
@Controller
public class HelloWorldController {

    @RequestMapping("/helloWorld")
    public String helloWorld(Model model) {
        model.addAttribute("message", "Hello World!");
        return "helloWorld";
    }
}
```
你可以看到，**@Contorller**和**@RequestMapping**注解允许灵活的方法名称和签名。在这个特殊例子中，方法接收一个**Model**并且以**String**返回一个视图名称，但也可以使用其他各种方法参数和返回值，本节稍后会介绍。**@Controller**和**@RequestMapping**以及一些其他注解构成了Spring MVC实现的基础。本节介绍这些注解以及它们在Servlet环境中最常用的方式。。

## 使用@Controller定义一个控制器
***
**@Controller**注解表示这个特定类用于 *控制器* 角色。Spring不要求你继承任何控制器基类或引用Servlet API。但是，如果有需要，你仍然可以引用Servlet特定的功能。

**@Controller**注解对于被注解的类作为模板，表明被注解类的角色。调度器扫描这些注解的类用于映射方法，并且自动探测**@RequestMapping**注解（请参阅下一节）。

你可以显示定义注解的控制器bean，通过在调度器的上下文中使用标准的Spring bean定义。然而，**@Controller**模型还允许自动检测，这与Spring对在类路径中探测组件类和从他们中自动注册bena定义的支持对齐。

要开启对这样的注解控制器自动探测的支持，你要向你的配置中添加组件扫描。如下面XML片段那样使用spring-context schema：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="org.springframework.samples.petclinic.web"/>

    <!-- ... -->

</beans>
```

## 使用@RequestMapping映射请求
***
你使用**@RequestMapping**注解将诸如/appointments的URL映射到整个类或指定的处理器方法上。通常，类级注解将特定请求路径（或路径模式）映射到表单控制器上，并且使用另外的方法级注解缩小了对特定HTTP方法的请求方法（“GET"，“POST"等）或HTTP请求参数条件的主映射。

Petcare示例中的以下例子显示了使用此注解的Spring MVC应用程序中的控制器：
```java
@Controller
@RequestMapping("/appointments")
public class AppointmentsController {

    private final AppointmentBook appointmentBook;

    @Autowired
    public AppointmentsController(AppointmentBook appointmentBook) {
        this.appointmentBook = appointmentBook;
    }

    @RequestMapping(method = RequestMethod.GET)
    public Map<String, Appointment> get() {
        return appointmentBook.getAppointmentsForToday();
    }

    @RequestMapping(path = "/{day}", method = RequestMethod.GET)
    public Map<String, Appointment> getForDay(@PathVariable @DateTimeFormat(iso=ISO.DATE) Date day, Model model) {
        return appointmentBook.getAppointmentsForDay(day);
    }

    @RequestMapping(path = "/new", method = RequestMethod.GET)
    public AppointmentForm getNewForm() {
        return new AppointmentForm();
    }

    @RequestMapping(method = RequestMethod.POST)
    public String add(@Valid AppointmentForm appointment, BindingResult result) {
        if (result.hasErrors()) {
            return "appointments/new";
        }
        appointmentBook.addAppointment(appointment);
        return "redirect:/appointments";
    }
}
```
在上面的例子中，**@RequestMapping**用在了很多地方。第一个用法是在类型（类）级别，它表示此控制器中的所有处理器方法都是相对于/appointments路径。get()方法上还有一个更细化的**@RequestMapping**：它只接收GET请求，这意味着对/appointments的HTTP GET会调用此方法。add()有一个类似的细化注解，而getNewForm()将HTTP方法和路径的定义组合成一个，这样对于appointments/new的GET请求将会由这个方法处理。

getForDay()方法显示了@RequestMapping的另一种用法：URI模板。（参见[“URI模板"](#URI模板)一节）。

在类级别上的**@RequestMapping**不是必须的。没有它，所有的路径都是绝对的，而不是相对。PetClinic示例应用程序的以下例子显示了使用**@RequestMapping**的multi-action控制器：
```java
@Controller
public class ClinicController {

    private final Clinic clinic;

    @Autowired
    public ClinicController(Clinic clinic) {
        this.clinic = clinic;
    }

    @RequestMapping("/")
    public void welcomeHandler() {
    }

    @RequestMapping("/vets")
    public ModelMap vetsHandler() {
        return new ModelMap(this.clinic.getVets());
    }

}
```
上面的例子没有指定GET与PUT，POST等等，因为**@RequestMapping**默认映射所有的HTTP方法。使用**@RequestMapping(method=GET)**或**@GetMapping** 来缩小这个映射.

### 组合@RequestMapping的变体
Spring框架4.3引入了**@RequestMapping**注解的以下方法级组合变体，这有助于简化常见HTTP方法的映射，并更好地表达注解处理器方法的语义。例如，**@GetMapping**可以被读取为一个**GET** **@RequestMapping**。
- **@GetMapping**
- **@PostMapping**
- **@PutMapping**
- **@DeleteMapping**
- **@PatchMapping**

以下示例通过组合的**@RequestMapping**注解进行简化，显示了上一节中的AppointmentsController的修改版本。
```java
@Controller
@RequestMapping("/appointments")
public class AppointmentsController {

    private final AppointmentBook appointmentBook;

    @Autowired
    public AppointmentsController(AppointmentBook appointmentBook) {
        this.appointmentBook = appointmentBook;
    }

    @GetMapping
    public Map<String, Appointment> get() {
        return appointmentBook.getAppointmentsForToday();
    }

    @GetMapping("/{day}")
    public Map<String, Appointment> getForDay(@PathVariable @DateTimeFormat(iso=ISO.DATE) Date day, Model model) {
        return appointmentBook.getAppointmentsForDay(day);
    }

    @GetMapping("/new")
    public AppointmentForm getNewForm() {
        return new AppointmentForm();
    }

    @PostMapping
    public String add(@Valid AppointmentForm appointment, BindingResult result) {
        if (result.hasErrors()) {
            return "appointments/new";
        }
        appointmentBook.addAppointment(appointment);
        return "redirect:/appointments";
    }
}
```
### @Controller和AOP代理
在某些情况下，控制器可能需要在运行时期用AOP代理进行装饰。一个例子是如果你选择在控制器上直接使用**@Transactional**注解。在这种情况下，对于控制器，我们建议使用基于类的代理。这通常是控制器的默认选择。但是，如果控制器必须实现一个不是Spring Context回调（例如**InitializingBean**，**\*Aware**等）的接口，你可能需要显示配置基于类的代理。例如，使**&lt;tx:annotation-driven/>**，更改为**&lt;tx:annotation-driven proxy-target-class ="true"/>**。

### Spring MVC 3.1中对@RequestMapping方法的新支持类
Spring 3.1为**@RequestMapping**方法引入了一组新的支持类，分别叫做**RequestMappingHandlerMapping**和**RequestMappingHandlerAdapter**。推荐使用它们，即使需要使用Spring MVC 3.1和之后版本的新功能。MVC命名空间和MVC Java配置默认启用这些新的支持类，但是如果你不使用，则必须显示配置。本节介绍旧支持类和新支持类之间的一些重要区别。

在Spring 3.1之前，类型和方法级请求映射是在两个不同的阶段进行了检查— 首先由**DefaultAnnotationHandlerMapping**选择一个控制器，然后通过**AnnotationMethodHandlerAdapter**缩小实际方法调用范围。

使用Spring 3.1中的新支持类，**RequestMappingHandlerMapping**是唯一可以决定哪个方法应该处理请求的地方。将这些控制器方法视为从类型和方法级**@RequestMapping**信息派生的每个方法的映射的唯一端点集合。

这开启了一些新的可能性。一旦**HandlerInterceptor**或**HandlerExceptionResolver**现在可以期望基于对象的处理器作为**HandlerMethod**，这就允许他们检查确切的方法，以及它的参数和相关注解。URL的处理就不再需要跨不同的控制器进行拆分。

还有几件事情已经不复存在了：
- 首先使用**SimpleUrlHandlerMapping**或**BeanNameUrlHandlerMapping**选择控制器，然后基于**@RequestMapping**注解来缩小方法。
- 依靠方法名称作为一种回退机制，来消除两个没有映射URL路径的明确路径，但是其他方面匹配（例如HTTP方法）的@RequestMapping方法之间的歧义。在新的支持类中，**@RequestMapping**方法必须被唯一地映射。
- 
如果没有其他控制器方法更具体地匹配话，使一个单独的默认方法（没有明确路径映射）来处理请求。在新的支持类中，如果一个匹配的方法都没有找到，就会产生一个404错误。

上面这些功能依旧被现有支持类支持。不过要利用新的Spring MVC 3.1功能，你需要使用新的支持类。

### URI模板
可以使用URI模板方便地访问**@RequestMapping**方法中URL的所选部分。

URI模板是一个类似URI的字符串，包含一个或多个变量名称。当你为这些变量替换值时，模板就变成了一个URI。URI模板的提议RFC定义了一个URI如何被参数化。例如，URI模板{%raw%}http://www.example.com/users/{userId} {%endraw%} 包含变量userId。如果将fred值分配给变量会得到{%raw%}http://www.example.com/users/fred{%endraw%} 。

在Spring MVC你可以在一个方法参数上使用**@PathVariable**注解，来绑定该参数到URI模板变量的值上：
```java
@GetMapping("/owners/{ownerId}")
public String findOwner(@PathVariable String ownerId, Model model) {
    Owner owner = ownerService.findOwner(ownerId);
    model.addAttribute("owner", owner);
    return "displayOwner";
}
```
URI模板 "/owners/{ownerId}"指定了变量名称为'owenerId'。当控制器处理这个请求时，会用在URI的适当部分中找到的值，来设置ownerId的值。例如，当进来一个 /owner/fred 请求时，ownerId的值为fred。

> 要处理**@PathVariable**注解，Spring MVC需要按名称找到匹配的URI模板变量。你可以在注解中指定它。
```java
@GetMapping("/owners/{ownerId}")
public String findOwner(@PathVariable("ownerId") String theOwner, Model model) {
    // 忽略实现
}
```
或者如果URI模板变量名称与方法参数名称匹配，则可以省略该详细信息。只要您的代码使用调试信息或Java 8上的-parameters编译器标记进行编译，Spring MVC会将方法参数与URI模板变量名称进行匹配：
```java
@GetMapping("/owners/{ownerId}")
public String findOwner(@PathVariable String ownerId, Model model) {
    // 忽略实现
}
```

一个方法可以有任意数量的@PathVariable注解：
```java
@GetMapping("/owners/{ownerId}/pets/{petId}")
public String findPet(@PathVariable String ownerId, @PathVariable String petId, Model model) {
    Owner owner = ownerService.findOwner(ownerId);
    Pet pet = owner.getPet(petId);
    model.addAttribute("pet", pet);
    return "displayPet";
}
```
当在Map&lt;String，String>参数上使用**@PathVariable**注解时，map会被填充上所有URI模板变量。

URI模板可以从类型和方法级**@RequestMapping**注解中进行组合。因此，可以使用/owner/42/pets/21等URL调用findPet()方法。
```java
@Controller
@RequestMapping("/owners/{ownerId}")
public class RelativePathUriTemplateController {

    @RequestMapping("/pets/{petId}")
    public void findPet(@PathVariable String ownerId, @PathVariable String petId, Model model) {
        // implementation omitted
    }

}
```
**@PathVariable**参数可以是任何简单的类型，如int，long，Date等。Spring会自动转换为适当的类型或抛出**TypeMismatchException**异常,如果Spring转换失败的话。您还可以注册解析其他数据类型的支持。请参见[“方法参数和类型转换"](#方法参数和类型转换)一节以及[“自定义WebDataBinder初始化"](#自定义WebDataBinder初始化)一节。

### 使用正则表达式的URI模板模式
有时你需要更精确地定义URI模板变量。考虑URL“/spring-web/spring-web-3.0.5.jar"。你怎么把它分解成多个部分？

**@RequestMapping**注解支持在URI模板变量中使用正则表达式。语法是**{varName:regex}**，其中第一部分定义了变量名，第二部分定义了正则表达式。例如：
```java
@RequestMapping("/spring-web/{symbolicName:[a-z-]+}-{version:\\d\\.\\d\\.\\d}{extension:\\.[a-z]+}")
public void handle(@PathVariable String version, @PathVariable String extension) {
    // ...
}
```
### 路径模式
除了URI模板，**@RequestMapping**注解和所有的**@RequestMapping**变体还支持Ant风格的路径模式（例如/myPath/\*.do）。还支持URI模板变量和Ant-style glob的组合（例如/owners/ \* /pets/{petId}）。

### 路径模式比较
当一个URL匹配多个模式时，使用排序来查找最具体的匹配。

具有较低数量的URI变量和通配符的模式被认为更具体。例如/hotels/{hotel}/\*有一个URI变量和一个通配符，它被认为比有一个URI变量和两个通配符的/hotels/{hotel}/\*\*更具体。

如果两个模式有相同的数量，那么更长的那个被认为更具体。例如，/foo/bar\*比/foo/\*更长，因此也被认为更具体。

当两个模式有相同的数量和长度，那么有更少通配符的会被认为更具体。例如/hotels/{hotel}比/hotels/\*更具体。

还有一些额外的特殊规则：
- **默认的映射模式**——/\*\* 比任何其他模式都更不具体。相比下/api/{a}/{b}/{c}更具体。
- **前缀模式**——例如/public/\*\*比任何其他不包含双通配符的模式都更不具体。例如相比下/public/path3/{a}/{b}/{c}更具体。

有关详细信息，请参阅**AntPathMatcher**中的**AntPatternComparator**。注意，**PathMatcher**可以进行自定义（参见章节16.11“配置Spring MVC"中的[第16.11节“路径匹配"](#路径匹配)）。

### 有占位符的路径模式
@RequestMapping注解中的模式支持对本地属性和/或系统属性和环境变量的$ {...}占位符。在一个控制器需要映射的路径可能需要通过配置自定义的情况下，这很有用。关于占位符的更多信息，请参阅PropertyPlaceholderConfigurer类的javadocs 。

### 后缀模式匹配
默认情况下，Spring MVC执行“.\*"后缀模式匹配，以便映射到/person的控制器也隐式映射到/person.\*上。这使得通过URL路径可以轻松地请求资源的不同表示（例如/person.pdf，/person.xml）。

后缀模式匹配可以关闭或限制为一组明确注册用于内容协商的路径扩展。通常建议通过使用诸如/person/{id}之类的普通请求映射来减少歧义，这里其中的点可能不表示文件扩展名，例如/person/joe@email.com vs /person/joe@email.com.json。此外，如下面的注释中所说明的，后缀模式匹配以及内容协商可能在某些情况下被用于尝试恶意攻击，因此有充分的理由有意义地限制它们。

对于后缀模式匹配请参见[第16.11，“路径匹配"](#路径匹配),对于内容协商配置请参见[第16.6，“内容协商"](#内容协商)。

### 后缀模式匹配和RFD
Trustwave在2014年的论文中首次描述了反射文件下载 — Reflected file download（RFD）攻击。攻击类似于XSS，因为它依靠在响应中反映的输入（例如查询参数，URI变量）。然而，相比将JavaScript插入到HTML中，RFD攻击依赖浏览器切换为之行一个下载或者酱响应视为一个可执行脚本，如果如果根据文件扩展名双击（例如.bat，.cmd）的话。

在Spring MVC中@ResponseBody和ResponseEntity方法存在风险，因为它们可以呈现不同的内容类型，客户端可以通过URL路径拓展请求包含它们。但是请注意，禁用后缀模式匹配或禁用仅用于内容协商的路径扩展都可以有效地防止RFD攻击。

为了全面防范RFD，在渲染响应体之前，Spring MVC添加了一个Content-Disposition:inline;filename=f.txt头来建议一个固定和安全的下载文件文件名。但只有当URL路径包含一个既不在白名单中，也不是用于内容协商的目的而注册的文件扩展名，才会这么做。然而，当URL直接输入浏览器时，这可能会产生一个副作用。

许多常见的路径扩展名默认为白名单。此外，REST API调用通常在浏览器中不是直接用作URL。然而，使用自定义HttpMessageConverter实现的应用程序可以明确地注册用于内容协商的文件扩展名，并且不会为此类扩展添加Content-Disposition头。见[第16.6节“内容协商"](#内容协商)。

> 这是CVE-2015-5211工作的一部分。以下是报告中的其他建议：
- 编码而不是转义JSON响应。这也是OWASP XSS的建议。有关Spring的例子，请参阅[spring-jackson-owasp](#https://github.com/rwinch/spring-jackson-owasp)。
- 将后缀模式匹配配置为关闭或仅限于明确注册的后缀。
- 使用将属性“useJaf"和“ignoreUnknownPathExtensions"设置为false来配置内容协商，这会对带有未知拓展名的URL响应一个406错误。如果URL自然希望在最后有一个点的话，这可能不是一个选择。
- 添加X-Content-Type-Options: nosniff头到响应。 Spring Security 4默认情况下执行此操作。

### 矩阵变量
URI规范RFC 3986定义了在路径片段中包含name-value对的可能性。规范中没有使用特定的术语。一般的“URI路径参数"可能适用，尽管来自Tim Berners-Lee的旧帖子的更独特的“Matrix URI"也经常被使用并且是相当熟知的。在Spring MVC中，这些被称为矩阵变量（Matrix Variables）。

矩阵变量可以出现在任何路径段中，每个矩阵变量用“;"（分号）分隔。例如：“/cars;color=red;year=2012"。多个值可以是“,"（逗号）分隔“color=red,green,blue"，或者变量名称可以重复“color=red; color=green; color=blue"。

如果URL期望包含矩阵变量，则请求映射模式必须使用URI模板来表示它们。这确保了请求可以正确匹配，无论矩阵变量是否存在，以及它们以什么顺序提供。

以下是提取矩阵变量“q"的示例：
```java
// GET /pets/42;q=11;r=22

@GetMapping("/pets/{petId}")
public void findPet(@PathVariable String petId, @MatrixVariable int q) {

    // petId == 42
    // q == 11

}
```
由于所有路径段都可能包含矩阵变量，因此在某些情况下，您需要更具体地确定变量预期位于何处：
```java
// GET /owners/42;q=11/pets/21;q=22

@GetMapping("/owners/{ownerId}/pets/{petId}")
public void findPet(
        @MatrixVariable(name="q", pathVar="ownerId") int q1,
        @MatrixVariable(name="q", pathVar="petId") int q2) {

    // q1 == 11
    // q2 == 22

}
```
矩阵变量可以定义为可选参数，并指定一个默认值：
```java
// GET /pets/42

@GetMapping("/pets/{petId}")
public void findPet(@MatrixVariable(required=false, defaultValue="1") int q) {

    // q == 1

}
```
所有矩阵变量可以在一个Map中获得：
```java
// GET /owners/42;q=11;r=12/pets/21;q=22;s=23

@GetMapping("/owners/{ownerId}/pets/{petId}")
public void findPet(
        @MatrixVariable MultiValueMap<String, String> matrixVars,
        @MatrixVariable(pathVar="petId") MultiValueMap<String, String> petMatrixVars) {

    // matrixVars: ["q" : [11,22], "r" : 12, "s" : 23]
    // petMatrixVars: ["q" : 11, "s" : 23]

}
```
请注意，为了使用矩阵变量，必须将**RequestMappingHandlerMapping**的**removeSemicolonContent**属性设置为**false**。默认设置为**true**。
> MVC Java配置和MVC命名空间都提供了使用矩阵变量的选项。
如果您使用Java配置，[使用MVC Java Config进行高级自定义](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#mvc-config-advanced-java)部分将介绍如何自定义**RequestMappingHandlerMapping**。    
在MVC命名空间中，**&lt;mvc:annotation-driven>**元素有**enable-matrix-variables**属性,它应该被设置为**true**。默认情况下它被设置为**false**。 
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <mvc:annotation-driven enable-matrix-variables="true"/>

</beans>
```

### Consumable Media Types
您可以通过指定Consumable Media Types的列表来缩小主要映射。只有当Content-Type请求头与指定的media type匹配时，才会匹配该请求。例如：
```java
@PostMapping(path = "/pets", consumes = "application/json")
public void addPet(@RequestBody Pet pet, Model model) {
    // 忽略实现
}
```
Consumable Media Types表达式也可以以**!text/plain**否定，以匹配所有**Content-Type**不是**text/plain**的请求。也可以考虑使用**MediaType**中提供的常量，例如**APPLICATION_JSON_VALUE**和**APPLICATION_JSON_UTF8_VALUE**。

> 在类型和方法级上支持consume条件。不想大多数其他条件，当用在类型级上，方法级consumable类型覆盖而不是拓展类型级consumable类型。

### Producible Media Types
您可以通过指定Producible Media Types列表来缩小主映射。只有当**Accept**请求头匹配这些值之一时，才会匹配该请求。此外，使用produce条件确保用于产生响应的实际内容类型与produce条件中指定的媒体类型相关。例如：
```java
@GetMapping(path = "/pets/{petId}", produces = MediaType.APPLICATION_JSON_UTF8_VALUE)
@ResponseBody
public Pet getPet(@PathVariable String petId, Model model) {
    // 忽略实现
}
```

> 请注意，在produce条件中指定的媒体类型还可以选择指定一个字符集。例如，在上面的代码片段中，我们指定与**MappingJackson2HttpMessageConverter**中配置的相同的媒体类型，包括UTF-8字符集。

就像consumes一样，producible媒体类型表达式可以以!text/plain否定，以匹配除为text/plain的Accept之外的所有请求。还要考虑使用MediaType中提供的常量，例如APPLICATION_JSON_VALUE和APPLICATION_JSON_UTF8_VALUE。

> 在类型和方法级上支持produces条件。不想大多数其他条件，当用在类型级上，方法级producible 类型覆盖而不是拓展类型级producible 类型。

### 请求参数和请求头值
你可以通过请求参数条件（如“myParam"，“!myParam"或​​“myParam=myValue"）缩小请求匹配。前两个测试请求参数存在/不存在，第三个为特定参数值。下面是一个请求参数值条件的例子：
```java
@Controller
@RequestMapping("/owners/{ownerId}")
public class RelativePathUriTemplateController {

    @GetMapping(path = "/pets/{petId}", params = "myParam=myValue")
    public void findPet(@PathVariable String ownerId, @PathVariable String petId, Model model) {
        // implementation omitted
    }

}
```
这同样也可用于对于测试请求头存在/不存在或基于指定的请求头值来匹配
```java
@Controller
@RequestMapping("/owners/{ownerId}")
public class RelativePathUriTemplateController {

    @GetMapping(path = "/pets", headers = "myHeader=myValue")
    public void findPet(@PathVariable String ownerId, @PathVariable String petId, Model model) {
        // implementation omitted
    }

}
```

> 虽然可以使用媒体类型通配符匹配Content-Type和Accept标头值（例如“content-type=text/\*"将匹配“text/plain"和“text/html"），但建议分别使用consumes和produces条件来代替。它们专门用于此目的。

### HTTP HEAD和HTTP OPTIONS
映射到“GET"的**@RequestMapping**方法也隐式映射到“HEAD"，即不需要明确声明“HEAD"。处理一个HTTP HEAD请求就和处理HTTP GET一样，除了它仅统计响应体的字节数，并且设置在"Content-Length" 响应头中，而不写入响应体。

**@RequestMapping**方法内置支持HTTP OPTIONS。默认情况下，HTTP OPTIONS请求通过在HTTP方法（明确声明在所有与URL模式匹配的@RequestMapping方法上的）上设置“Allow"响应头来处理。当没有HTTP方法被显示声明“Allow"，头被设置为"GET,HEAD,POST,PUT,PATCH,DELETE,OPTIONS"。理想地总是声明**@RequestMapping**方法要处理的HTTP方法，或者使用专用的组合**@RequestMapping**变体之一（请参阅[“组合@RequestMapping变体"](#组合@RequestMapping变体)一节）。


尽管不需要**@RequestMapping**方法可以映射到HTTP HEAD或HTTP OPTIONS，或同时两者。

## 定义@RequestMapping处理器方法
***
**@RequestMapping**处理器方法可以有非常灵活的签名。支持的方法参数和返回值将在以下部分中介绍。大多数参数可以按任意顺序使用，唯一的例外是BindingResult参数。这将在下一节中介绍。

> Spring 3.1分别为**@RequestMapping**方法引入了一组新的支持类，分别叫做**RequestMappingHandlerMapping**和**RequestMappingHandlerAdapter**推荐使用它们，即使需要使用Spring MVC 3.1和之后版本的新功能。MVC命名空间和MVC Java配置默认启用这些新的支持类，但是如果你不使用，则必须明确配置


### 支持的方法参数类型
以下是支持的方法参数类型：
- **Request**或**response**对象（Servlet API）。选择任何具体的request或response类型，例如ServletRequest或HttpServletRequest。
- **Session**对象（Servlet API）：类型为**HttpSession**。这个类型的参数强制存在相应的session。因此，这样的参数从不为**null**。
> **Session**访问可能不是线程安全的，特别在Servlet环境中。如果允许多个request同时访问session，请考虑将**RequestMappingHandlerAdapter**的“synchronizeOnSession"标志设置为“true"。

- **org.springframework.web.context.request.WebRequest**或**org.springframework.web.context.request.NativeWebRequest**。允许通用请求参数访问以及request/session属性访问，与本地Servlet/Portlet API无关。
- **java.util.Locale**——当前的请求语言环境，由最具体的语言环境解析器确定，实际上是在MVC环境中配置的LocaleResolver/LocaleContextResolver。
- **java.util.TimeZone**(Java 6+)/**java.time.ZoneId**(Java 8)——当前请求相关联的时区，由LocaleContextResolver确定。
- **java.io.InputStream/java.io.Reader**——访问请求的内容。该值是由Servlet API暴露的原生InputStream/Reader。
- **java.io.OutputStream/java.io.Writer**——用于生成响应的内容。该值是由Servlet API暴露的原生 OutputStream/Writer。
- **org.springframework.http.HttpMethod**——HTTP请求方法。
- **java.security.Principal**——包含当前验证的用户
- **@PathVariable**注解参数——用于访问URI模板变量。请参阅[“URI模板模式"](#URI模板模式)一节。
- **@MatrixVariable**注解参数——用于访问位于URI路径段中的name-value对。请参阅[“矩阵变量"](#矩阵变量)一节。
- **@RequestParam**注解参数——用于访问特定的Servlet请求参数。参数值将转换为声明的方法参数类型。请参阅[“使用@RequestParam绑定请求参数到方法参数"](#使用@RequestParam绑定请求参数到方法参数)一节。
- **@RequestHeader**注解参数——用于访问特定的Servlet请求头。参数值将转换为声明的方法参数类型。请参阅[“使用@RequestHeader注解映射请求头属性"](#使用@RequestHeader注解映射请求头属性)一节。
- **@RequestBody**注解参数——用于访问HTTP请求体。使用HttpMessageConverters将参数值转换为声明的方法参数类型。请参阅[“使用@RequestBody注解映射请求体"](#使用@RequestBody注解映射请求体)一节。
- **@RequestPart**注解参数——用于访问“multipart/form-data"请求部分的内容。请参见[第10.5节“从程序化客户端处理文件上传请求"](#从程序化客户端处理文件上传请求)和[第10节“Spring的多部分（文件上传）支持"）](#Spring的多部分（文件上传）支持)。
- **@SessionAttribute**注解参数——用于访问现有,永久的session属性（例如，用户认证对象），而不是访问作为控制器工作流程一部分通过@SessionAttributes临时存储在session中的model属性。
- **@RequestAttribute**注解参数——用于访问请求属性。
- **HttpEntity&lt;?>**参数——用于访问Servlet请求HTTP头和内容。请求流将使用HttpMessageConverters转换为实体。请参阅[“使用HttpEntity"](#使用HttpEntity)一节。
- **java.util.Map** / **org.springframework.ui.Model** / **org.springframework.ui.ModelMap**——用于富化暴露给Web视图的隐式model。
- **org.springframework.web.servlet.mvc.support.RedirectAttributes**在重定向情况下指定确切的属性集来使用，并且还添加flash属性（临时存储在服务器端的属性，使其在重定向之后可用于请求）。请参见(“将数据传递到重定向目标"](#将数据传递到重定向目标)一节和(第22.6节“使用flash属性"](#使用flash属性)一节。
- 根据**@InitBinder**方法和/或**HandlerAdapter**配置，使用自定义的类型转换，命令或表单对象将请求参数绑定到bean属性（通过setter）或直接转到字段上。请参阅**RequestMappingHandlerAdapter**上的**webBindingInitializer**属性。默认情况下，这些命令对象及其验证结果将作为model属性公开，使用命令类名称-例如。对于“some.package.OrderAddress"类型的命令对象的model属性“orderAddress"。**ModelAttribute**注解可以用于方法参数来自定义所使用的model属性名称。
- **org.springframework.validation.Errors/org.springframework.validation.BindingResult**验证前面的命令或表单对象的结果（即在前面的方法参数）。
- **org.springframework.web.bind.support.SessionStatus**用于标记表单处理完成的状态处理，这会触发在处理器类型级别上由@SessionAttributes注解指示的session属性的清理。
- **org.springframework.web.util.UriComponentsBuilder**——用于准备一个与当前请求的 host, port, scheme, context path以及servlet映射的文字部分相对的URL的构建器。

**Errors**或**BindingResult**参数必须跟在被绑定的模型对象后，因为方法签名可能有多个模型对象，Spring将为每个模型对象创建一个单独的BindingResult实例，因此以下示例将无法工作：

*BindingResult和@ModelAttribute无效排序。*
```java
@PostMapping
public String processSubmit(@ModelAttribute("pet") Pet pet, Model model, BindingResult result) { ... }
```
注意，在Pet和BindingResult之间有一个Model参数。要使其工作，你必须将参数按如下重新排序：
```java
@PostMapping
public String processSubmit(@ModelAttribute("pet") Pet pet, BindingResult result, Model model) { ... }
```
> JDK 1.8的java.util.Optional也被支持作为带有注解的方法参数类型，该注解有一个required属性（例如**@RequestParam**，**@RequestHeader**等）在这些情况下使用java.util.Optional相当于有有一个属性required = false。

### 支持的方法返回类型
以下是支持的方法返回类型：
- 一个**ModelAndView**对象，带有使用命令对象和**@ModelAttribute**注解引用的数据访问器方法的结果隐式富化的model。
- 一个**Model**对象，视图名称通过**RequestToViewNameTranslator**隐式确定，model使用命令对象和@ModelAttribute注解引用的数据访问器方法的结果隐式富化。
- 用于暴露model的**Map**对象，视图名称通过**RequestToViewNameTranslator**隐式确定，model使用命令对象和@ModelAttribute注解引用的数据访问器方法的结果隐式富化。
- 一个**View**对象，model通过命令对象和**@ModelAttribute**注解引用数据访问器方法隐式确定。这个处理器方法也可以通过声明一个Model参数（见上文）以编程方式富化model。
- 一个**String**值，它被解释为逻辑视图名，model通过命令对象和**@ModelAttribute**注解引用数据访问器方法隐式确定。这个处理器方法也可以通过声明一个Model参数（见上文）以编程方式富化model。
- **void**，如果方法自己处理响应（通过直接写入响应内容，为此目的声明一个ServletResponse/HttpServletResponse类型的参数）或者视图名称应该通过RequestToViewNameTranslator隐式确定（不在处理器方法签名中声明响应参数）。
- 如果方法被@ResponseBody注解，返回的类型被写入到响应HTTP体中。返回值将使用HttpMessageConverters转换为声明的方法参数类型。请参阅[“使用@ResponseBody注解映射响应体"](#使用@ResponseBody注解映射响应体)一节。
- 一个**HttpEntity&lt;>**或**ResponseEntity&lt;>**对象,以提供对Servlet响应HTTP头和内容的访问。实体将使用HttpMessageConverters转换为响应流。请参阅[“使用HttpEntity"](#使用HttpEntity)一节。
- 一个**HttpHeaders**对象，它返回没有响应体的响应。
- 一个**Callable&lt;?>**，当应用程序想要在由Spring MVC管理的线程中异步生成返回值时，可以返回它。
- 一个**DeferredResult&lt;?>**，当应用程序想从自己选择​​的线程生成返回值时，可以返回它。
- 一个**ListenableFuture&lt;?>**或**CompletableFuture&lt;?>**/**CompletionStage&lt;?>**当应用程序想要从线程池提交中产生值时，可以返回它们
- 一个**ResponseBodyEmitter**，可以返回它以异步地将多个对象写入响应;也支持作为ResponseEntity内的主体。
- 一个**SseEmitter**，可以返回它以将异步的Server-Sent事件写入响应;也支持作为ResponseEntity内的主体。
- 一个**StreamingResponseBody**，可以返回它以异步写入响应OutputStream;;也支持作为ResponseEntity内的主体。
- 任何其他返回类型都被认为是要暴露给视图的单个model属性，使用在方法级通过@ModelAttribute指定的属性名称（或基于返回类型类名的默认属性名称）。model使用命令对象和@ModelAttribute注解引用的数据访问器方法的结果隐式富化。

### 使用@RequestParam将请求参数绑定到方法参数
在控制器中使用**@RequestParam**注解将请求参数绑定到方法参数

以下代码片段显示用法：
```java
@Controller
@RequestMapping("/pets")
@SessionAttributes("pet")
public class EditPetForm {

    // ...

    @GetMapping
    public String setupForm(@RequestParam("petId") int petId, ModelMap model) {
        Pet pet = this.clinic.loadPet(petId);
        model.addAttribute("pet", pet);
        return "petForm";
    }

    // ...

}
```
使用这个注解的参数默认是必须的，但是你可以通过设置**@RequestParam**的属性**required**为false来指定这个参数为可选（例如**@RequestParam(name =“id"，required = false)**）

如果目标方法参数类型不是String，则会自动应用类型转换。请参阅[“方法参数和类型转换"](#方法参数和类型转换)一节。

当在**Map&lt;String,String>**或**MultiValueMap&lt;String,String>**参数上使用**@RequestParam**注解时，map将被填充所有请求参数。

### 使用@RequestBody注解映射请求体
**@RequestBody**方法参数注解表示方法参数应绑定到HTTP请求体的值上。例如：
```java
@PutMapping("/something")
public void handle(@RequestBody String body, Writer writer) throws IOException {
    writer.write(body);
}
```
你通过使用**HttpMessageConverter**将请求体转换为方法参数。**HttpMessageConverter**负责将HTTP请求消息转换为对象，并从对象转换为HTTP响应体。**RequestMappingHandlerAdapter**以以下默认**HttpMessageConverters**支持**@RequestBody**注解：
- **ByteArrayHttpMessageConverter**转换字节数组。
- **StringHttpMessageConverter**转换字符串。
- **FormHttpMessageConverter**将表单数据从到 MultiValueMap&lt;String,String>转换。
- **SourceHttpMessageConverter**与**javax.xml.transform.Source**互相转换。

有关这些转换器的更多信息，请参阅["消息转换器"](#消息转换器)。另请注意，如果使用MVC命名空间或MVC Java配置，默认情况下会注册更广泛的消息转换器。有关详细信息，请参见[第16.1节“启用MVC Java配置或MVC XML命名空间"](#启用MVC Java配置或MVC XML命名空间)。

如果您打算读写XML，则需要使用**org.springframework.oxm**包中的具体的**Marshaller**和**Unmarshaller**实现配置**MarshallingHttpMessageConverter**。下面的示例显示了如何直接在配置中执行此操作，但是如果您的应用程序通过MVC命名空间或MVC Java配置进行配置，请参阅[第16.1节“启用MVC Java配置或MVC XML命名空间"](#启用MVC Java配置或MVC XML命名空间)。

```xml
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
    <property name="messageConverters">
        <util:list id="beanList">
            <ref bean="stringHttpMessageConverter"/>
            <ref bean="marshallingHttpMessageConverter"/>
        </util:list>
    </property>
</bean>

<bean id="stringHttpMessageConverter"
        class="org.springframework.http.converter.StringHttpMessageConverter"/>

<bean id="marshallingHttpMessageConverter"
        class="org.springframework.http.converter.xml.MarshallingHttpMessageConverter">
    <property name="marshaller" ref="castorMarshaller"/>
    <property name="unmarshaller" ref="castorMarshaller"/>
</bean>

<bean id="castorMarshaller" class="org.springframework.oxm.castor.CastorMarshaller"/>
```

**@RequestBody**方法参数可以使用**@Valid**注解，在这种情况下，它将使用配置的**Validator**实例进行验证。当使用MVC命名空间或MVC Java配置时，会自动配置一个JSR-303验证器，假设在类路径上有可用的JSR-303实现。

就像**@ModelAttribute**参数一样，可以使用一个**Errors**参数来检查错误。如果未声明此类参数，则将引发**MethodArgumentNotValidException**异常。这个异常在**DefaultHandlerExceptionResolver**中处理，它将向客户端发送一个400错误。

> 有关通过MVC命名空间或MVC Java配置配置消息转换器和验证器的信息，请参见[第16.1节“启用MVC Java配置或MVC XML命名空间"](#启用MVC Java配置或MVC XML命名空间)。

### 使用@ResponseBody注解映射响应体
**@ResponseBody**注解类似于**@RequestBody**。这个注解可以被放置在方法上，并表示返回值类型应该被直接写入HTTP响应体中（而不是放在Model中，或者解释为视图名称）。例如
```java
@GetMapping("/something")
@ResponseBody
public String helloWorld() {
    return "Hello World";
}
```
上面的例子会导致文本Hello World被写入HTTP响应流中。
与**@RequestBody**一样，Spring通过使用**HttpMessageConverter**将返回的对象转换为响应体。有关这些转换器的更多信息，请参阅上一部分和[消息转换器](#消息转换器)。

### 使用@RestController注解创建REST控制器
控制器实现REST API是一个非常常见的用例，从而仅提供JSON，XML或自定义的MediaType内容。为了方便起见，相比使用**@ResponseBody**注解你的所有的**@RequestMapping**方法，你可以使用**@RestController**注解你的控制器类。

**@RestController**是一个结合了**@ResponseBody**和**@Controller**的模型注解。更重要的是，它给您的控制器提供了更多的意义，并且可能在将来的框架版本中携带额外的语义。
与常规的**@Controller**一样，**@RestController**可以由**@ControllerAdvice**或@**RestControllerAdvice **beans协助。有关更多详细信息，请参阅[“使用@ControllerAdvice和@RestControllerAdvice通知控制器"](#使用@ControllerAdvice和@RestControllerAdvice通知控制器)一节。

### 使用HttpEntity
**HttpEntity**类似于**@RequestBody**和**@ResponseBody**。除了访问请求和响应体之外，**HttpEntity**（以及响应特定的子类**ResponseEntity**）还允许访问请求和响应头，如下所示：
```java
@RequestMapping("/something")
public ResponseEntity<String> handle(HttpEntity<byte[]> requestEntity) throws UnsupportedEncodingException {
    String requestHeader = requestEntity.getHeaders().getFirst("MyRequestHeader"));
    byte[] requestBody = requestEntity.getBody();

    // 使用请求头和请求体执行某些操作

    HttpHeaders responseHeaders = new HttpHeaders();
    responseHeaders.set("MyResponseHeader", "MyValue");
    return new ResponseEntity<String>("Hello World", responseHeaders, HttpStatus.CREATED);
}
```
以上示例获取**MyRequestHeader**请求头的值，并将字节数组读取形式读取请求体。在响应中添加了**MyResponseHeader**，将Hello World写入到响应流中，并且设置响应代码未201（Created）。

与**@RequestBody**和**@ResponseBody**一样，Spring使用**HttpMessageConverter**从请求和响应流转换。有关这些转换器的更多信息，请参阅上一部分和[消息转换器](#消息转换器)。

### 在方法上使用@ModelAttribute
**@ModelAttribute**注解可以在方法和方法参数上使用。这节说明它的在方法上的用法，下一节说明它在方法参数上的用法。

在方法上的**@ModelAttribute**表示该方法的目的是添加一个或多个**Model**属性。这样的方法支持与**@RequestMapping**方法相同的参数类型，但不能直接映射到请求上。在同一个控制器中，控制器中的**@ModelAttribute**方法在**@RequestMapping**方法之前被调用。几个例子：
```java
// 添加一个属性
// 方法的返回值类型会被在"account“名称下添加到model中
// 你可以通过@ModelAttribute（“myAccount"）自定义名称

@ModelAttribute
public Account addAccount(@RequestParam String number) {
    return accountManager.findAccount(number);
}

// 添加多个属性

@ModelAttribute
public void populateModel(@RequestParam String number, Model model) {
    model.addAttribute(accountManager.findAccount(number));
    // 添加更多 ...
}
```
**@ModelAttribute**方法用于在model中填充常用属性，例如使用state或pet类型填充下拉列表，或者检索诸如Account的命令对象，以便使用它来表示HTML表单上的数据。后一种情况在下一节进一步讨论。

注意两种样式的**@ModelAttribute**方法。在第一个方法中，该方法通过返回隐式地添加一个属性。在第二个方法中，该方法接受一个**Model**并向它添加任意数量的model属性。你可以根据在两种样式中选择。

控制器可以有任何数量的**@ModelAttribute**方法。在同一控制器所有这些方法都在**@RequestMapping**方法之前调用。

**@ModelAttribute**方法也可以在**@ControllerAdvice**注解的类中定义，而这种方法也适用于许多控制器。有关更多详细信息，请参阅[“使用@ControllerAdvice和@RestControllerAdvice通知控制器"](#使用@ControllerAdvice和@RestControllerAdvice通知控制器)一节。

> 当没有明确指定model属性名称时会发生什么？在这种情况下，根据其类型将默认名称分配给model属性。例如，如果该方法返回一个类型为Account的对象，使用的默认名称是"account"。你可以通过**@ModelAttribute**注解的值来更改它。如果直接向model中添加属性，请使用合适的**addAttribute(..)**重载方法，即带有或不带有属性名称。

**@ModelAttribute**注解也可以在**@RequestMapping**方法中使用。在这种情况下，**@RequestMapping**方法的返回值将被解释为model属性而不是视图名称。然后视图名称根据视图名称惯例推导出，这非常类似于返回void的方法 - 见第[13.3节“视图 - RequestToViewNameTranslator"](#视图 - RequestToViewNameTranslator)。

### 在方法参数上使用@ModelAttribute
如上一节所述，**@ModelAttribute**可以在方法或方法参数上使用。本节介绍了其在方法参数中的用法。

方法参数上的**@ModelAttribute**表示这个参数应该从model中检索。如果model中不存在，参数首先被实例化，然后添加到model中。一旦model中存在，这个参数的字段会从所有名称相匹配的请求参数中填充。这在Spring MVC中被称为的数据绑定，这是一个非常有用的机制，可以节省您逐个解析每个表单字段时间。
```java
@PostMapping("/owners/{ownerId}/pets/{petId}/edit")
public String processSubmit(@ModelAttribute Pet pet) { }
```
上面给定的示例中，Pet示例可以才能够哪里获得？这有几个选项：
- 由于**@SessionAttributes**的使用，它可能已经在model中— 请参阅[“使用@SessionAttributes在请求之间的HTTP session中存储模型属性"](#使用@SessionAttributes在请求之间的HTTP session中存储模型属性)一节。
- 由于同一控制器中的**@ModelAttribute**方法，它可能已经在model中- 如前一节所述。
- 它可以基于URI模板变量和类型转换器（下面更详细地解释）来检索。
- 它可以使用其默认构造函数实例化。

**@ModelAttribute**方法是从数据库检索属性的常用方式，可以通过使用**@SessionAttributes**来选择性地存储在请求之间。在某些情况下，通过使用URI模板变量和类型转换器来检索属性可能很方便。下面一个例子：
```java
@PutMapping("/accounts/{account}")
public String save(@ModelAttribute("account") Account account) {
    // ...
}
```
在此示例中，model属性（即“account"）的名称与URI模板变量的名称相匹配。如果你注册可以将**String**类型的account值转换为**Account**实例的**Converter&lt;String,Account>**，那么上面的例子不需要@ModelAttribute方法也可以工作。

下一步是数据绑定。**WebDataBinder**类按照名称将请求参数名称（包括查询字符串参数和表单字段）匹配到模型属性字段上。匹配的字段在必要的类型转换（从String到目标字段类型）应用后被填充。数据绑定和验证在第9章"验证，数据绑定，和类型转换“中一经介绍。自定义控制器级别的数据绑定过程将在[“自定义WebDataBinder初始化"](#自定义WebDataBinder初始化)一节中介绍。

数据绑定可能会出现错误，比如缺少必须字段或类型转换错误。要检查这样的错误，请在**@ModelAttribute**参数之后立即添加**BindingResult**参数：
```java
@PostMapping("/owners/{ownerId}/pets/{petId}/edit")
public String processSubmit(@ModelAttribute("pet") Pet pet, BindingResult result) {

    if (result.hasErrors()) {
        return "petForm";
    }

    // ...

}
```
使用**BindingResult**可以检查是否发现错误，在通常呈现相同表单的情况吓,这里可以在Spring的&lt;errors>表单标签的帮助下显示错误。

注意，在某些情况下，不使用数据绑定来获取访问modle中的属性可能是有用的。对于这种情况，您可以将**Model**注入控制器，或者作为选择的在注解上使用**binding**标志：
```java
@ModelAttribute
public AccountForm setUpForm() {
    return new AccountForm();
}

@ModelAttribute
public Account findAccount(@PathVariable String accountId) {
    return accountRepository.findOne(accountId);
}

@PostMapping("update")
public String update(@Valid AccountUpdateForm form, BindingResult result,
        @ModelAttribute(binding=false) Account account) {

    // ...
}
```
除了数据绑定之外，你还可以使用你自己的自定义验证器传递相同的用于记录数据绑定错误的**BindingResult**来调用验证。这允许数据绑定和验证的错误在同一个地方累积，并随后向用户报告：
```java
@PostMapping("/owners/{ownerId}/pets/{petId}/edit")
public String processSubmit(@ModelAttribute("pet") Pet pet, BindingResult result) {

    new PetValidator().validate(pet, result);
    if (result.hasErrors()) {
        return "petForm";
    }

    // ...

}
```
或者您可以通过添加JSR-303 **@Valid**注解来自动调用验证：
```java
@PostMapping("/owners/{ownerId}/pets/{petId}/edit")
public String processSubmit(@Valid @ModelAttribute("pet") Pet pet, BindingResult result) {

    if (result.hasErrors()) {
        return "petForm";
    }

    // ...

}
```
有关如何配置和使用验证的详细信息，请参见第9.8节[“Spring验证"](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#validation-beanvalidation)和[第9章“验证，数据绑定和类型转换“](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#validation)。

### 使用@SessionAttributes在请求之间的HTTP session中存储模型属性
类型级**@SessionAttributes**注解声明特定处理器使用的session属性。这通常将列出模型属性名称或模型属性的类型，这些模型属性或类型应该透明地存储在会话或某些会话存储中，作为后续请求之间的表单支持bean。

下面的代码片段展示了这个注解的用法，指定模型属性名称：
```java
@Controller
@RequestMapping("/editPet.do")
@SessionAttributes("pet")
public class EditPetForm {
    // ...
}
```
### 使用@SessionAttribute访问预先存在的全局session属性
如果您需要访问全局管理的预先存在的session属性，即控制器之外的（如一个过滤器），并且它可能或不存在，则可以在方法参数上使用**@SessionAttribute**注解：
```java
@RequestMapping("/")
public String handle(@SessionAttribute User user) {
    // ...
}
```
对于需要添加或删除session属性的用例，请考虑将**org.springframework.web.context.request.WebRequest**或**javax.servlet.http.HttpSession**注入到控制器方法中。

对于作为控制器工作流程中的一部分，在session中的模型属性的临时存储，请考虑使用**SessionAttributes**，如上面“使用@SessionAttributes在请求之间的HTTP session中存储模型属性"一节中所述。

### 使用@RequestAttribute来访问请求属性
与**@SessionAttribute**类似，**@RequestAttribute**注解可用于访问由过滤器或拦截器创建的预先存在的请求属性：
```java
@RequestMapping("/")
public String handle(@RequestAttribute Client client) {
    // ...
}
```

### 使用“application/x-www-form-urlencoded"数据
前面的部分介绍了使用**@ModelAttribute**来支持来自浏览器客户端的表单提交请求。建议来自非浏览器客户端的请求使用同样的注解。然而，在使用HTTP PUT请求时，会有一个显着的区别。浏览器可以通过HTTP GET或HTTP POST提交表单数据。非浏览器客户端还可以通过HTTP PUT提交表单。这提出了一个挑战，因为Servlet规范要求**ServletRequest.getParameter *()**系列方法仅支持HTTP POST的表单字段访问，而不支持HTTP PUT。

为了支持HTTP PUT和PATCH请求，**spring-web**模块提供了过滤器**HttpPutFormContentFilter**，它可以在**web.xml**中配置：
```xml
<filter>
    <filter-name>httpPutFormFilter</filter-name>
    <filter-class>org.springframework.web.filter.HttpPutFormContentFilter</filter-class>
</filter>

<filter-mapping>
    <filter-name>httpPutFormFilter</filter-name>
    <servlet-name>dispatcherServlet</servlet-name>
</filter-mapping>

<servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
</servlet>
```
上述过滤器拦截内容类型为**application/x-www-form-urlencoded**的HTTP PUT和PATCH请求，从请求的正文中读取表单数据，并包装成**ServletRequest**，以便可以通过**ServletRequest.getParameter \*()**系列方法使用表单数据。

> 由于**HttpPutFormContentFilter**消耗了请求的正文，所以不应该为PUT或PATCH URLs配置依赖其他的按**application/x-www-form-urlencoded**的转换器。这包括**@RequestBody MultiValueMap&lt;String，String>**和**HttpEntity&lt;MultiValueMap&lt;String，String >>**。

### 使用@CookieValue注解映射Cookie值
**@CookieValue**注解允许将方法参数被绑定到HTTP cookie的值上。

让我们考虑下面从一个http请求中接收的cookie：
*JSESSIONID=415A4AC178C59DACE0B2C9CA727CDD84*
以下代码示例演示如何获取JSESSIONID cookie的值：
```java
@RequestMapping("/displayHeaderInfo.do")
public void displayHeaderInfo(@CookieValue("JSESSIONID") String cookie) {
    //...
}
```
如果目标方法参数类型不是String，则会自动应用类型转换。请参阅[“方法参数和类型转换"](#方法参数和类型转换)一节。

Servlet和Portlet环境中的注解处理器方法支持此注解。

### 使用@RequestHeader注解映射请求头属性
**@RequestHeader**注解允许将一个方法参数绑定到一个请求头上。

以下是一个示例请求头：
```
Host                    localhost:8080
Accept                  text/html,application/xhtml+xml,application/xml;q=0.9
Accept-Language         fr,en-gb;q=0.7,en;q=0.3
Accept-Encoding         gzip,deflate
Accept-Charset          ISO-8859-1,utf-8;q=0.7,*;q=0.7
Keep-Alive              300
```

以下代码示例演示了如何获取**Accept-Encoding**和**Keep-Alive**头的值：
```java
@RequestMapping("/displayHeaderInfo.do")
public void displayHeaderInfo(@RequestHeader("Accept-Encoding") String encoding,
        @RequestHeader("Keep-Alive") long keepAlive) {
    //...
}
```
如果方法参数类型不是String，则会自动应用类型转换。请参阅[“方法参数和类型转换"](#方法参数和类型转换)一节。

当在**Map&lt;String,String>**，**MultiValueMap&lt;String,String>**或**HttpHeaders**参数上使用**@RequestHeader**注解时，map会被填充所有头值。

> 内置支持可用于将一个逗号分割的字符串转换为一个字符串或类型转换系统已知的其他类型的数组/集合。例如，使用@RequestHeader（“Accept"）注解的方法参数可以是String类型，但也可以是String []或List <String>。

Servlet和Portlet环境中的注解处理器方法支持此注解。

### 方法参数和类型转换
从请求中提取的基于字符串的值包括请求参数，路径变量，请求头和cookie值，它们可能需要转换为它们要绑定到的方法参数或字段的目标类型（例如，将请求参数绑定到@ModelAttribute参数中的字段）。如果目标类型不是String，则Spring将自动转换为适当的类型。所有简单的类型，如int，long，Date等都被支持。你可以通过**WebDataBinder**进一步自定义转换过程（请参阅“自定义WebDataBinder初始化"一节），或者使用**FormattingConversionService**注册**Formatter**（参见第9.6节“Spring字段格式化"）。

### 自定义WebDataBinder初始化
要通过Spring的**WebDataBinder**定制使用**PropertyEditors**的请求参数绑定，你可以在你的控制器中使用**@InitBinder**注解方法，在**@ControllerAdvice**类中使用**@InitBinder**方法，或提供自定义**WebBindingInitializer**。有关更多详细信息，请参阅“使用@ControllerAdvice和@RestControllerAdvice的通知控制器"一节。

### 使用@InitBinder自定义数据绑定
使用**@InitBinder**的注解方法允许你直接在你的控制器类中配置web数据绑定。**@InitBinder**标识初始化**WebDataBinder**的方法，**WebDataBinder**将用于填充注解处理器方法的命令和表单对象参数。

这种init-binder方法支持@RequestMapping方法支持的所有参数，除了命令/表单对象和相应的验证结果对象。Init-binder方法不能有返回值。因此，它们通常被声明为void。典型的参数包括WebDataBinder与WebRequest或java.util.Locale的组合，允许代码注册上下文特定的编辑器。

以下示例演示如何使用**@InitBinder**为所有**java.util.Date**表单属性配置**CustomDateEditor**。
```java
@Controller
public class MyFormController {

    @InitBinder
    protected void initBinder(WebDataBinder binder) {
        SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");
        dateFormat.setLenient(false);
        binder.registerCustomEditor(Date.class, new CustomDateEditor(dateFormat, false));
    }

    // ...
}
```
或者，从Spring 4.2起，考虑使用**addCustomFormatter**来指定**Formatter**实现而不是**PropertyEditor**实例。如果你恰好在一个共享的**FormattingConversionService**中有一个基于**Formatter**的设置，这个会特别有用，用相同的方式重用控制器特定的绑定规则的调整。
```java
@Controller
public class MyFormController {

    @InitBinder
    protected void initBinder(WebDataBinder binder) {
        binder.addCustomFormatter(new DateFormatter("yyyy-MM-dd"));
    }

    // ...
}
```

### 配置一个自定义的WebBindingInitializer
要在外部执行数据绑定初始化，你可以提供一个**WebBindingInitializer**接口的自定义实现，然后通过为**AnnotationMethodHandlerAdapter**提供自定义bean配置来启用它，从而覆盖默认配置。

PetClinic应用程序的以下示例显示了使用**WebBindingInitializer**接口（org.springframework.samples.petclinic.web.ClinicBindingInitializer）的自定义实现的配置，它配置了几个PetClinic控制器所需的PropertyEditor。
```xml
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
    <property name="cacheSeconds" value="0"/>
    <property name="webBindingInitializer">
        <bean class="org.springframework.samples.petclinic.web.ClinicBindingInitializer"/>
    </property>
</bean>
```
**@InitBinder**方法也可以在**@ControllerAdvice**注解的类中定义，在这种情况下，它们适用于匹配控制器。这提供了使用WebBindingInitializer的替代方法。有关更多详细信息，请参阅[“使用@ControllerAdvice和@RestControllerAdvice通知控制器"](#使用@ControllerAdvice和@RestControllerAdvice通知控制器)一节。

### 使用@ControllerAdvice和@RestControllerAdvice通知控制器
@ControllerAdvice注解是组件注解，允许通过类路径扫描自动检测实现类。当使用MVC命名空间或MVC Java配置时，它将自动启用。

使用**@ControllerAdvice**注解的类可以包含**@ExceptionHandler**，**@InitBinder**和**@ModelAttribute**注解方法，并且这些方法将应用于所有控制器层次结构中的**@RequestMapping**方法，而不是声明它们的控制器层次结构。

**@RestControllerAdvice**是默认使用**@ResponseBody**语义的**@ExceptionHandler**方法的替代方案。

**@ControllerAdvice**和**@RestControllerAdvice**都可以针对控制器的一个子集：
```java
// 针对所有使用@RestController注释的Controller
@ControllerAdvice(annotations = RestController.class)
public class AnnotationAdvice {}

// 针对特定包中的所有Controller
@ControllerAdvice("org.example.controllers")
public class BasePackageAdvice {}

// 针对可分配给指定类的所有Controller
@ControllerAdvice(assignableTypes = {ControllerInterface.class, AbstractController.class})
public class AssignableTypesAdvice {}
```
查看@ControllerAdvice[文档](http://docs.spring.io/spring-framework/docs/4.3.10.RELEASE/javadoc-api/org/springframework/web/bind/annotation/ControllerAdvice.html)了解更多详细信息。

### Jackson序列化视图支持
有时根据上下文过滤将被序列化到HTTP响应主体的对象是有用的。为了提供这样的功能，Spring MVC内置了对Jackson的Serialization Views进行渲染的支持。

要将它与使用@ResponseBody控制器方法或返回ResponseEntity的控制器方法一起使用，只需使用指定要使用的视图类或接口的类参数添加@JsonView注解：
```java
@RestController
public class UserController {

    @GetMapping("/user")
    @JsonView(User.WithoutPasswordView.class)
    public User getUser() {
        return new User("eric", "7!jd#h23");
    }
}

public class User {

    public interface WithoutPasswordView {};
    public interface WithPasswordView extends WithoutPasswordView {};

    private String username;
    private String password;

    public User() {
    }

    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }

    @JsonView(WithoutPasswordView.class)
    public String getUsername() {
        return this.username;
    }

    @JsonView(WithPasswordView.class)
    public String getPassword() {
        return this.password;
    }
}
```
> 请注意，尽管@JsonView允许指定多个类，但用在控制器方法上只支持一个类参数。如果需要启用多个视图，请考虑使用复合接口。

对于依赖于视图解析的控制器，只需将序列化视图类添加到模型中：
```java
@Controller
public class UserController extends AbstractController {

    @GetMapping("/user")
    public String getUser(Model model) {
        model.addAttribute("user", new User("eric", "7!jd#h23"));
        model.addAttribute(JsonView.class.getName(), User.WithoutPasswordView.class);
        return "userView";
    }
}
```
### Jackson JSONP支持
为了启用对**@ResponseBody**和**ResponseEntity**方法的JSONP支持，声明一个**@ControllerAdvice** bean，它继承自**AbstractJsonpResponseBodyAdvice**，如下所示，这里构造函数参数表示JSONP查询参数的名称：
```java
@ControllerAdvice
public class JsonpAdvice extends AbstractJsonpResponseBodyAdvice {

    public JsonpAdvice() {
        super("callback");
    }
}
```
对于依赖于视图解析的控制器，当请求具有名为**jsonp**或**callback**的查询参数时，将自动启用JSONP。这些参数名称可以通过jsonpParameterNames属性自定义。

## 异步请求处理
***
Spring MVC 3.2引入了基于Servlet 3的异步请求处理。相比往常返回一个值，控制器方法现在可以返回一个java.util.concurrent.Callable并从Spring MVC管理的线程生成返回值。与此同时，Servlet容器主线程被退出和释放并且允许处理其他请求。Spring MVC在TaskExecutor的帮助下在一个单独的线程中调用Callable，当Callable返回时，该请求将被分派回到Servlet容器，以使用这个Callable对象的返回值来恢复处理这个请求。这有一个这样一个控制器方法的例子：
```java
@PostMapping
public Callable<String> processUpload(final MultipartFile file) {

    return new Callable<String>() {
        public String call() throws Exception {
            // ...
            return "someView";
        }
    };

}
```
这个控制器方法也可以是返回一个DeferredResult的实例。在这种情况下，返回值也将从任何线程生成，即不由Spring MVC管理的线程。例如，结果可能是由于某些外部事件而产生的，例如JMS消息，计划任务等。这有一个这样一个控制器方法的例子：
```java
@RequestMapping("/quotes")
@ResponseBody
public DeferredResult<String> quotes() {
    DeferredResult<String> deferredResult = new DeferredResult<String>();
    // 将deferredResult保存在某个地方..
    return deferredResult;
}

// 在其他线程中...
deferredResult.setResult(data);
```
在不了解任何Servlet 3.0异步请求处理功能知识的情况下，这可能很难理解。阅读这些肯定会有帮助。以下是有关底层机制的几个基本事实：
- 可以通过调用**request.startAsync()**将**ServletRequest**置于异步模式。这样做的主要作用是Servlet以及任何Filter都可以退出，但响应将保持开放状态，以便稍后完成处理。
- 对**request.startAsync()**的调用返回**AsyncContext**，它可用于进一步控制异步处理。例如，它提供了**dispatch**方法，除了允许应用程序在Servlet容器线程上恢复请求处理这点外，它类似于Servlet API的转发。
- **ServletRequest**提供对当前**DispatcherType**的访问，它可用于区分处理初始请求，异步调度，转发和其他调度器类型。

考虑到上述情况，以下是使用**Callable**进行异步请求处理的事件序列：
- 控制器返回一个**Callable**。
- Spring MVC启动异步处理，并将**Callable**提交给**TaskExecutor**，以在单独的线程中进行处理。
- **DispatcherServlet**和所有**Filter**都退出Servlet容器线程，但响应保持打开状态。
- **Callable**产生一个结果，Spring MVC将请求返回给Servlet容器以恢复处理。
- **DispatcherServlet**再次被调用，并且从**Callable**异步生成的结果开始继续处理。

这与**DeferredResult**的顺序非常相似，除了它可以在应用程序中任何线程生成异步结果：
- 控制器返回一个**DeferredResult**并将其保存在某些可以访问的内存中的queue或list 中。
- Spring MVC启动异步处理。
- **DispatcherServlet**和所有**Filter**都退出Servlet容器线程，但响应保持打开状态。
- 应用程序从一些线程设置DeferredResult，Spring MVC将请求发回到Servlet容器。
- **DispatcherServlet**再次被调用，并且从异步生成的结果开始继续处理。

### 异步请求的异常处理
如果从控制器方法返回的**Callable**在执行时引发异常，会发生什么？简答是这与控制器方法引发异常时发生的情况一样。它经历了常规异常处理机制。更长的解释是，当**Callable**引发一个异常，Spring MVC以这个**Exception**为结果调度回Servlet容器，并导致以异常而不是控制器方法返回值为开始恢复请求处理。当使用**DeferredResult**时，您可以选择是否使用**Exception**实例调用**setResult**或**setErrorResult**。

### 拦截异步请求
**HandlerInterceptor**为了实现**afterConcurrentHandlingStarted**回调，还可以实现**AsyncHandlerInterceptor**，当异步处理开始时，这个回调被调用而不是**postHandle**和**afterCompletion**。

**HandlerInterceptor**还可以注册**CallableProcessingInterceptor**或**DeferredResultProcessingInterceptor**，以便更深入地整合异步请求的生命周期，例如处理超时事件。有关更多详细信息，请参阅**AsyncHandlerInterceptor**的Javadoc。

**DeferredResult**类型同样提供了诸如**onTimeout(Runnable)**和**onCompletion(Runnable)**等方法。有关详细信息，请参阅DeferredResult的Javadoc。

当使用**Callable**时，您可以使用**WebAsyncTask**的实例来包装它，该实例还提供了timeout和completion的注册方法。

### HTTP Streaming
控制器方法可以使用DeferredResult和Callable异步生成其返回值，并且可以用于实现诸如[long polling](https://spring.io/blog/2012/05/08/spring-mvc-3-2-preview-techniques-for-real-time-updates/)的技术，这里服务器可以尽快将事件推送到客户端。

如果你想在单个HTTP响应中推送多个事件怎么办？这是一个与“Long Polling”相关的技术，也被称为“HTTP Streaming”。Spring MVC可以通过**ResponseBodyEmitter**返回值类型来实现，该类型可以用于发送多个对象，而不是像**@ResponseBody**的一样，发送的每个对象都使用**HttpMessageConverter**写入响应。

这有一个示例：
```java
@RequestMapping("/events")
public ResponseBodyEmitter handle() {
    ResponseBodyEmitter emitter = new ResponseBodyEmitter();
    // 将emitter保存在某处..
    return emitter;
}

// 在其他一些线程
emitter.send("Hello once");

// 并且之后再一次
emitter.send("Hello again");

// 并在某些时候完成
emitter.complete();
```
请注意，**ResponseBodyEmitter**也可以用作**ResponseEntity**中的正文，以便自定义响应的状态和响应头

### HTTP Streaming与Server-Sent Events
SseEmitter是ResponseBodyEmitter的子类，提供对[Server-Sent Events](https://www.w3.org/TR/eventsource/)的支持。Server-sent event只是同一个“HTTP Streaming”技术的另一个变体，除了从服务器推送的事件根据 W3C Server-Sent Events规范进行格式化的。

Server-Sent Events可用于预期的用途，即将事件从服务器推送到客户端。在Spring MVC中这很容易做到，只需返回一个SseEmitter类型的值。

请注意，Internet Explorer不支持Server-Sent Events，而对于更高级的Web应用程序消息传递场景（如在线游戏，协作，财务应用程序和其他），最好考虑Spring的WebSocket支持，它包括SockJS风格的WebSocket模拟，能够广泛地支持各种浏览器（包括Internet Explorer），同时支持在以消息作为核心的应用架构中，使用发布-订阅模型来在客户端间进行交互的更高层次的消息模式。如需了解更多，请参阅[这个博客的文章](http://blog.pivotal.io/pivotal/products/websocket-architecture-in-spring-4-0)。

### HTTP Streaming直接写到OutputStream中
 **ResponseBodyEmitter**可以使用**HttpMessageConverter**来把对象写入到响应中，并发送这个事件。比如:写入JSON数据这种较为普遍的情况。然而，有些时候，比如像文件下载这种，分流消息的转换和直接写到相应的OutputStream中是很有用的。可以通过**StreamingResponseBody**返回的值类型来实现。
 
下面是一个例子:
```java
@RequestMapping("/download")
public StreamingResponseBody handle() {
    return new StreamingResponseBody() {
        @Override
        public void writeTo(OutputStream outputStream) throws IOException {
            // write...
        }
    };
}
```
请注意，**ResponseBodyEmitter**也同样能作为**ResponseEntity**的主体，来自定义响应的status（状态）和报头。
	  
### 配置异步的请求处理
#### Servlet容器配置
对于使用web.xml配置的应用程序，请确保升级到了3.0，如下所示:
```xml
<web-app xmlns="http://java.sun.com/xml/ns/javaee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            http://java.sun.com/xml/ns/javaee
            http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
    version="3.0">

    ...

</web-app>
```
异步支持必须在**DispatcherServlet**上开启,通过**web.xml**中的**&lt;async-supported>true&lt;/async-supported>**子元素。另外，任何参与异步请求处理的**Filter**都必须配置为支持ASYNC dispatcher类型。为Spring Framework提供的所有过滤器启用ASYNC dispatcher类型应该是安全的，因为它们通常继承自**OncePerRequestFilter**，并且具有关于过滤器是否需要参与异步调度的运行时期检查。

以下是一些web.xml配置示例：
```xml
<web-app xmlns="http://java.sun.com/xml/ns/javaee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
            http://java.sun.com/xml/ns/javaee
            http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
    version="3.0">

    <filter>
        <filter-name>Spring OpenEntityManagerInViewFilter</filter-name>
        <filter-class>org.springframework.~.OpenEntityManagerInViewFilter</filter-class>
        <async-supported>true</async-supported>
    </filter>

    <filter-mapping>
        <filter-name>Spring OpenEntityManagerInViewFilter</filter-name>
        <url-pattern>/*</url-pattern>
        <dispatcher>REQUEST</dispatcher>
        <dispatcher>ASYNC</dispatcher>
    </filter-mapping>

</web-app>
```
如果使用Servlet 3，基于Java的配置，例如通过**WebApplicationInitializer**，你同样需要像**web.xml**一样设置“asyncSupported”标志以及ASYNC dispatcher 类型。为了简化所有这些配置，请考虑继承**AbstractDispatcherServletInitializer**或更好的**AbstractAnnotationConfigDispatcherServletInitializer**，它会自动设置这些选项，并使其注册**Filter**实例变得非常简单。

#### Spring MVC配置
MVC Java配置和MVC命名空间提供了配置异步请求处理的选项。**WebMvcConfigurer**有**configureAsyncSupport**方法，而**&lt;mvc:annotation-driven>**有一个**&lt;async-support>**子元素。

这些允许你配置用于异步请求的默认超时值，如果未设置，则取决于底层的Servlet容器（例如Tomcat上为10秒）。你还可以配置一个AsyncTaskExecutor用于执行从控制器方法返回的Callable实例。强烈建议配置此属性，因为默认情况下，Spring MVC使用**SimpleAsyncTaskExecutor**。MVC的Java配置和MVC命名空间还允许你注册**CallableProcessingInterceptor**和**DeferredResultProcessingInterceptor**实例。


如果需要覆盖特定**DeferredResult**的默认超时值，可以使用适当的类构造函数。类似地，对于**Callable**，你可以将其包装在**WebAsyncTask**中，并使用适当的类构造函数自定义超时值。**WebAsyncTask**的类构造函数也允许提供一个**AsyncTaskExecutor**。

## 测试控制器
spring-test模块对于测试带注解的控制器提供一流的支持。请参见[第15.6节“Spring MVC测试框架”](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#spring-mvc-test-framework)。


***
# 处理器映射
***
在以前的Spring版本中，用户需要在Web应用程序上下文中定义一个或多个HandlerMapping bean，以将传入的Web请求映射到适当的处理器。随着注解控制器的引入，你一般不需要再这样做，因为**RequestMappingHandlerMapping**会自动在所有的**@Controller** bean上查找**@RequestMapping**注解。但是，请记住，从**AbstractHandlerMapping**扩展的所有**HandlerMapping**类都具有以下可用于自定义其行为的属性：

- **interceptors**：要使用的拦截器列表。HandlerInterceptors在第4.1节“使用HandlerInterceptor拦截请求”中讨论。
- **defaultHandler**：当映射器处理器在一次映射处理器时没有映射结果，默认使用的处理器，
- **order**：基于order属性的值（请参阅org.springframework.core.Ordered接口），Spring会排序上下文中可用的所有处理器映射器，并应用第一个匹配的处理器。
- **alwaysUseFullPath**：如果为true，Spring将使用当前Servlet上下文中的完整路径来查找适当的处理程序。如果为false（默认值），则使用当前Servlet映射中的路径。例如，如果一个Servlet使用/testing/\*映射，并且将alwaysUseFullPath属性设置为true，则使用/testing/viewPage.html，而如果属性设置为false，则使用/viewPage.html。
- **urlDecode**：默认为true，从Spring 2.5开始。如果您喜欢比较编码路径，请将此标志设置为false。但是，HttpServletRequest始终以解码形式公开Servlet路径。请注意，当与编码路径比较时，Servlet路径将不匹配。

以下示例说明如何配置拦截器
```xml
<beans>
    <bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping">
        <property name="interceptors">
            <bean class="example.MyInterceptor"/>
        </property>
    </bean>
<beans>
```

## 用HandlerInterceptor拦截请求
***
Spring的处理器映射机制包括处理器拦截器，当你希望将特定功能应用于某些请求时很有用，例如，检查主体。

位于处理器映射器中的拦截器必须实现**org.springframework.web.servlet**包中**HandlerInterceptor**。这个接口定义了三个方法：**preHandle(..)**在处理器实际执行之前调用;**postHandle(..)**在处理器执行后调用;**afterCompletion(..)**在完整的请求完成后调用。这三种方法应提供足够的灵活性进行各种预处理和后处理。

**preHandle(..)**方法返回一个布尔值。你可以使用此方法来中断或继续处理执行链。当此方法返回true时，处理器执行链将继续;当它返回**false**时，**DispatcherServlet**假定拦截器本身已经处理了请求（例如渲染了适当的视图），并且不会继续执行其他拦截器和执行链中的实际处理器。

拦截器可以使用interceptors属性进行配置，该属性存在于从**AbstractHandlerMapping**继承的所有HandlerMapping类上。这在下面的示例中显示：
```xml
<beans>
    <bean id="handlerMapping"
            class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping">
        <property name="interceptors">
            <list>
                <ref bean="officeHoursInterceptor"/>
            </list>
        </property>
    </bean>

    <bean id="officeHoursInterceptor"
            class="samples.TimeBasedAccessInterceptor">
        <property name="openingTime" value="9"/>
        <property name="closingTime" value="18"/>
    </bean>
</beans>
```
```java
package samples;

public class TimeBasedAccessInterceptor extends HandlerInterceptorAdapter {

    private int openingTime;
    private int closingTime;

    public void setOpeningTime(int openingTime) {
        this.openingTime = openingTime;
    }

    public void setClosingTime(int closingTime) {
        this.closingTime = closingTime;
    }

    public boolean preHandle(HttpServletRequest request, HttpServletResponse response,
            Object handler) throws Exception {
        Calendar cal = Calendar.getInstance();
        int hour = cal.get(HOUR_OF_DAY);
        if (openingTime <= hour && hour < closingTime) {
            return true;
        }
        response.sendRedirect("http://host.com/outsideOfficeHours.html");
        return false;
    }
}
```
由此映射器处理的任何请求都将被**TimeBasedAccessInterceptor**拦截。如果当前时间在办公时间之外，用户将被重定向到静态HTML文件，例如，您只能在办公时间内访问该网站。

> 当使用**RequestMappingHandlerMapping**时，实际的处理器是**HandlerMethod**的一个实例，它标识将被调用的特定控制器方法。

你可以看到，Spring适配器类**HandlerInterceptorAdapter**使得更容易扩展**HandlerInterceptor**接口。

> 在上面的示例中，配置的拦截器将应用于使用注解控制器方法处理的所有请求。如果要缩小拦截器应用的URL路径，你可以使用MVC命名空间或MVC Java配置，或声明类型为MappedInterceptor的bean实例来执行此操作。请参见第[16.1节“启用MVC Java配置或MVC XML命名空间”](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#mvc-config-enable)。

请注意，**HandlerInterceptor的postHandle**方法并不总是适用于**@ResponseBody**和**ResponseEntity**方法。在这种情况下，**HttpMessageConverter**在调用**postHandle**之前写入并提交响应，这使得不可能再更改响应，例如添加一个响应头。相反，应用程序可以实现**ResponseBodyAdvice**，并将其声明为**@ControllerAdvice** bean或直接在**RequestMappingHandlerAdapter**上进行配置。

***
# 解析视图
***
用于Web应用程序的所有MVC框架提供了一种解决视图的方法。Spring提供视图解析器，它使你可以在浏览器中渲染模型，而不需要将你和特定的视图技术捆绑起来。开箱即用，例如，Spring可以使用JSP，Velocity模板和XSLT视图。有关如何集成和使用多种不同视图技术的讨论，请参阅第[“视图技术”]()。

对于Spring处理视图的方式来说，两个重要的接口是**ViewResolver**和**View**。**ViewResolver**提供了视图名称和实际视图之间的映射。**View**接口解决了请求的准备，并将请求转交给其中一种视图技术。

## 使用ViewResolver接口解析视图
***
如[第3节“实现控制器”](#实现控制器)中所述，Spring Web MVC控制器中的所有处理器方法必须解析为逻辑视图名称,要么明确指出（例如，通过返回**String**，**View**或**ModelAndView**）要么隐式地（即基于约定）。Spring中的视图由逻辑视图名称编址，并由视图解析器解析。Spring自带很少几个视图解析器。这张表列出了大部分;以下几个例子。这张表列出了大部分;以下几个例子

|ViewResolver|描述|
|-----------|---------|
|**AbstractCachingViewResolver**|缓存视图的抽象视图解析器。通常视图在它能使用前需要准备;扩展此视图解析器提供缓存。|
|**XmlViewResolver**|ViewResolver的实现，它使用与Spring的XML bean工厂相同的DTD来接受用XML编写的配置文件。默认配置文件为/WEB-INF/views.xml。|
|**ResourceBundleViewResolver**|ViewResolver的实现，它使用ResourceBundle中的bean定义，由bundle基本名称指定。|
|**UrlBasedViewResolver**||
|**InternalResourceViewResolver**||
|**VelocityViewResolver / FreeMarkerViewResolver**||
|**ContentNegotiatingViewResolver**||










The ViewResolver provides a mapping between view names and actual views.