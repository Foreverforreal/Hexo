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
<!-- more -->

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

***
# DispatcherServlet
***
Spring的Web MVC框架与许多其他Web MVC框架一样，请求驱动，围绕一个中央Servlet设计，将请求分发给控制器，并提供了其他促进Web应用程序开发的功能。然而，Spring的DispatcherServlet做的不仅仅是这些。它完全域Srping Ioc容器集成，这样允许你使用那个Spring有的其他任何功能。

Spring Web MVC DispatcherServlet的请求处理工作流程如下图所示。精通模式的读者会意识到DispatcherServlet是“前端控制器”设计模式的表现（这是Spring Web MVC与许多其他领先的Web框架共享的一种模式）。   

*Spring Web MVC（高级别）中的请求处理工作流程*
![mvc](/images/spring/mvc.png)

DispatcherServlet实际上是一个Servlet（它继承自HttpServlet基类），并因此在你的web应用程序中声明。你需要通过使用URL映射，来映射你想要DispatcherServlet处理的请求。这有一个在Serlvet 3.0+ 环境中标准的Java EE Servlet配置。
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

WebApplicationInitializer是一个由Spring MVC提供的接口，它确保你的基于代码的配置被检测到，并且自动用于初始化任何Servlet 3 容器。一个这个接口的抽象基类实现名称叫AbstractAnnotationConfigDispatcherServletInitializer，它通过简单地指定其servlet映射和列出配置类来更容易地注册DispatcherServlet-它是设置你的Spring MVC应用程序最推荐的方式。更多详细信息，请参阅[“基于代码的Servlet容器初始化”](#基于代码的Servlet容器初始化)。

DispatcherServlet实际上是一个Servlet（它继承自HttpServlet基类），并因此在你的web应用程序的web.xml中声明。你需要在相同的web.xml文件中通过使用URL映射，来映射你想要DispatcherServlet处理的请求。这是标准的Java EE Servlet配置。以下示例显示了这样的DispatcherServlet的声明和映射：

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
如第7.15节[“ApplicationContext的附加功能”](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#context-introduction)中所述，Spring中的ApplicationContext实例可以被限定作用域。在Web MVC框架中，每个DispatcherServlet都有它自己的WebApplicationContext，这个上下文继承了在根WebApplicationContext中定义的所有bean。根WebApplicationContext应该包含应该在其他上下文和Servlet实例之间共享的所有的基础框架bean。这些继承的bean可以在特定servlet域内被覆盖，并且你可以在给定的Servlet实例本地定义一个新的特定域bean。

*Spring Web MVC中的典型上下文层次结构*
![mvc](/images/spring/mvc-context-hierarchy.png)
在初始化DispatcherServlet时，Spring MVC将在Web应用程序的WEB-INF目录中查找名为[servlet-name] -servlet.xml的文件，并创建在那里定义的bean，覆盖在全局域内任何使用相同名称定义的bean的定义。

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
随着上面的Servlet配置到位，你会需要在你的应用程序中有一个名为/WEB-INF/golfing-servlet.xml的文件；这个文件将包含你的所有Spring Web MVC特定组件（beans）。你可以通过Servlet初始化参数更改此配置文件的确切位置（有关详细信息，请参阅下文）。

单个DispatcherServlet方案也可能只有一个根上下文。

*Spring Web MVC中的单个根上下文*
![mvc](/images/spring/mvc-root-context.png)
这可以通过设置一个空的ContextConfigLocation servlet init参数进行配置，如下所示：
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
WebApplicationContext是普通ApplicationContext的扩展，它有一些Web应用程序所需的额外功能。它与普通的ApplicationContext不同之处在于它能够解析主题（参见[第22.9节“使用主题”](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#mvc-themeresolver)），并且知道它与哪个Servlet相关联（通过链接到ServletContext）。WebApplicationContext绑定在ServletContext中，并且如果你需要访问它，可以通过在RequestContextUtils类上使用静态方法，随时查找WebApplicationContext。

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

## 默认DispatcherServlet配置
***
如上一节中针对每个特殊bean所述，DispatcherServlet会维护默认使用的实现列表。这些信息保存在包org.springframework.web.servlet中的文件DispatcherServlet.properties中。

所有的特殊bean都有一些合理的默认值。虽然迟早你需要自定义一个或多个这些bean提供的属性。例如，配置一个InternalResourceViewResolver，设置他的prefix属性味视图文件的父路径是很常见的。

不管具体细节，需要明白的一个重要概念是，一旦你在你的WebApplicationContext中配置特殊bean（例如InternalResourceViewResolver ），你有效地覆盖了该特殊bean类型的默认实现列表。例如，如果配置了InternalResourceViewResolver，默认的ViewResolver实现列表会被忽视。

在第16节[“配置Spring MVC”](#配置Spring MVC)中，你将了解配置Spring MVC的其他选项，包括MVC Java配置和MVC XML命名空间，这两个都提供了一个简单的起点，这假定你对Spring MVC的工作原理并不太了解。无论你选择如何配置应用程序，本节中介绍的概念都是基本的，应该对你有所帮助。

## DispatcherServlet处理序列
***
在你设置了一个DispatcherServlet并且针对该特定DispatcherServlet启动了一个请求后，DispatcherServlet将按如下所示开始处理请求：
- WebApplicationContext被搜索和绑定到request上作为一个属性，这样控制器和其他元素在处理过程中可以使用。默认它被绑定到DispatcherServlet.WEB\_APPLICATION\_CONTEXT\_ATTRIBUTE这个key上。
- 语言环境解析器被绑定到request上，这样使处理过程中的元素能够解析语言环境设置来在处理request时使用（渲染视图，准备数据等）。如果您不需要语言环境解析，则不需要它。
- 主题解析器被绑定到request上，使元素（如视图）决定要使用哪个主题。如果不使用主题，可以忽略它。
- 如果你指定了multiparts文件解析器，则会检查该request的multipart;如果找到multipart，request会被包装在一个MultipartHttpServletRequest中，以便处理过程中的其他元素进一步处理。有关multiparts处理的更多信息，请参见[第10节“Spring的multipart（文件上传）支持”](#Spring的multipart（文件上传）支持)。
- 搜索适当的处理器。如果找到一个处理器，与处理器关联的执行链（预处理器，后处理器和控制器）被执行，以便准备 一个model或rendering。
- 如果返回一个modle，试图会被渲染。如果没有model返回，（可能是由于预处理器或后处理器基于安全原因拦截请求）则没有视图会被渲染，因为请求可能已经被完成了。

处理器异常解析器被声明在WebApplicationContext中，它可以提取在处理请求的过程中抛出的异常。使用这些异常解析器使你可以定义自定义的行为来解决异常。

Spring DispatcherServlet同样支持由Servlet API指定的last-modification-date的返回。对于指定请求确定最后修改日期的过程很简单：DispatcherServlet查找一个适当的处理器映射器，并检验发现的处理器是否实现了LastModified接口。如果是的话，LastModified接口long getLastModified(request)方法的值被返回给客户端。

你可以通过向web.xml文件中的Servlet声明添加Servlet初始化参数（init-para元素）来自定义单独的DispatcherServlet实例。有关支持的参数列表，请参见下表。

*DispatcherServlet的初始化参数*

|参数|解释|
|----|----|
|contextClass|实现了WebApplicationContext的类，它实例化了这个Servlet使用的上下文。默认情况下，使用XmlWebApplicationContext。|
|contextConfigLocation|传递给上下文实例（由contextClass指定）的字符串，以指示那里可以找到上下文。该字符串可能包含多个字符串（使用逗号作为分隔符）来支持多个上下文。在多个上下文位置中定义了一个bean两次，那么最后位置定义的优先|
|namespace|WebApplicationContext的命名空间。默认为[servlet-name] -servlet。|

***
# 实现控制器
***
控制器提供对应用程序行为的访问，这些行为你通常通过一个service接口定义。控制器解释用户输入并将其转换为一个通过视图变现给给用户的模型。Spring以一个非常抽象的方式实现控制器，使您能够创建各种各样的控制器。

Spring 2.5为MVC控制器引入了基于注解的编程式模型，它使用诸如@RequestMapping，@RequestParam，@ModelAttribute等注解。此注解支持可用于Servlet MVC和Portlet MVC。以这种样式实现的控制器不必扩展特定的基类或实现特定的接口。此外，它们通常不直接依赖于Servlet或Portlet API，尽管您可以轻松配置对Servlet或Portlet设施的访问。

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
你可以看到，@Contorller和@RequestMapping注解允许灵活的方法名称和签名。在这个特殊例子中，方法接受一个Model并且以String返回一个视图名称，但也可以使用其他各种方法参数和返回值，本节稍后会介绍。@Controller和@RequestMapping以及一些其他注解构成了Spring MVC实现的基础。本节介绍这些注解以及如何它们在Servlet环境中最常用使用。

## 使用@Controller定义一个控制器
***
@Controller注解表示这个特定类用于*控制器*角色。Spring不要求你继承任何控制器基类或引用Servlet API。然而，如果有需要，你仍然可以引用Servlet特定的功能。

@Controller注解作为被注解的类的构造型，表明它的角色。dispatcher为了映射方法和自动探测@RequestMapping注解（请参阅下一节），扫描这些被注解类。

你可以明确定义注解控制器bean，通过在dispatcher的上下文中使用标准的Spring bean定义。然而，@Controller模型还允许自动检测，这与Spring对在类路径中探测组件类和从他们中自动注册bena定义的支持对齐。

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
你使用@RequestMapping注解将诸如/appointments的URL映射到整个类或指定的处理器方法上。通常，类级注解将特定请求路径（或路径模式）映射到表单控制器上，并且使用另外的方法级注解缩小了对特定HTTP方法请求方法（“GET”，“POST”等）或HTTP请求参数条件的主映射。

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
在上面的例子中，@RequestMapping用在了很多地方。第一个用法是在类型（类）级别，她表示此控制器中的所有处理器方法都是相对于/appointments路径。get()方法上还有一个更细化的@RequestMapping：它只接收GET请求，这意味着对/appointments的HTTP GET会调用此方法。add()有一个类似的细化注解，而getNewForm()将HTTP方法和路径的定义组合成一个，这样对于appointments/new的GET请求将会由这个方法处理。

getForDay()方法显示了@RequestMapping的另一种用法：URI模板。（参见[“URI模板模式”](#URI模板模式)一节）。

在类级别上的@RequestMapping不是必须的。没有它，所有的路径都是绝对的，而不是相对。PetClinic示例应用程序的以下例子显示了使用@RequestMapping的multi-action控制器：
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
上面的例子没有指定GET与PUT，POST等等，因为@RequestMapping默认映射所有的HTTP方法。使用@RequestMapping(method=GET) or @GetMapping to narrow the mapping.

### 组合@RequestMapping变体
Spring框架4.3引入了@RequestMapping注解的以下方法级组合变体，这有助于简化常见HTTP方法的映射，并更好地表达注解处理器方法的语义。
- @GetMapping
- @PostMapping
- @PutMapping
- @DeleteMapping
- @PatchMapping

以下示例通过组合的@RequestMapping注解进行简化，显示了上一节中的AppointmentsController的修改版本。
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
在某些情况下，控制器可能需要在运行时用AOP代理进行装饰。一个例子是如果您选择在控制器上直接使用@Transactional注解。在这种情况下，对于控制器，我们建议使用基于类的代理。这通常是控制器的默认选择。但是，如果控制器必须实现不是Spring Context回调的接口（例如InitializingBean，*Aware等），你可能需要明确配置基于类的代理。例如，使用&lt;tx:annotation-driven/>，更改为&lt;tx:annotation-driven proxy-target-class =“true”/>。

### Spring MVC 3.1中对@RequestMapping方法的新支持类
Spring 3.1为@RequestMapping方法引入了一组新的支持类，分别叫做RequestMappingHandlerMapping和RequestMappingHandlerAdapter。推荐使用它们，即使需要使用Spring MVC 3.1和未来版本的新功能。MVC命名空间和MVC Java配置默认启用这些新的支持类，但是如果你不使用，则必须明确配置。本节介绍旧支持类和新支持类之间的一些重要区别。

在Spring 3.1之前，类型和方法级请求映射在两个不同的阶段进行了检查— 首先由DefaultAnnotationHandlerMapping选择一个控制器，然后通过AnnotationMethodHandlerAdapter缩小实际方法调用范围。

使用Spring 3.1中的新支持类，RequestMappingHandlerMapping是唯一可以决定哪个方法应该处理请求的地方。将这些控制器方法视为映射每个由类型和方法级@RequestMapping信息派生的方法的唯一端点集合。

这开启了一些新的可能性。