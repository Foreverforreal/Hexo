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
[Bob Martin, The Open-Closed Principle (PDF)](https://www.cs.duke.edu/courses/fall07/cps108/papers/ocp.pdf)
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

这开启了一些新的可能性。一旦一个HandlerInterceptor或HandlerExceptionResolver现在可以期望基于对象的处理器作为HandlerMethod，这就允许他们检查确切的方法，其参数和相关注解。URL的处理就不再需要跨不同的控制器进行拆分。

还有几件事情已经不复存在了：
- 首先使用SimpleUrlHandlerMapping或BeanNameUrlHandlerMapping选择控制器，然后基于@RequestMapping注解来缩小方法。
- 依靠方法名称作为一种回退机制，以消除两个没有映射URL路径的明确路径，但是其他方面匹配（例如HTTP方法）的@RequestMapping方法之间的歧义。在新的支持类中，@RequestMapping方法必须被唯一地映射。
- 如果没有其他控制器方法更具体地匹配，有一个单个默认方法（没有显示的路径映射）处理请求。在新的支持类中，如果一个匹配的方法没有找到，就会产生一个404错误。

上面这些功能依旧被现有支持类支持。不过要利用新的Spring MVC 3.1功能，你需要使用新的支持类。

### URI模板
可以使用URI模板方便地访问@RequestMapping方法中URL的所选部分

URI模板是一个类似URI的字符串，包含一个或多个变量名称。当你为这些变量替换值时，模板就变成了一个URI。URI模板的提议RFC定义了一个URI如何被参数化。例如，URI模板http://www.example.com/users/{userId}包含变量userId。将fred的值分配给变量会得到http://www.example.com/users/fred。

在Spring MVC你可以在一个方法参数上使用@PathVariable注解来绑定它到URI模板变量的值上：
```java
@GetMapping("/owners/{ownerId}")
public String findOwner(@PathVariable String ownerId, Model model) {
    Owner owner = ownerService.findOwner(ownerId);
    model.addAttribute("owner", owner);
    return "displayOwner";
}
```
URI模板 " /owners/{ownerId}`"指定了变量名称为'owenerId'。当控制器处理这个请求时，ownerId的值设置为在URI的适当部分中找到的值。例如，当进来一个/owner /fred请求时，ownerId的值为fred。

> 要处理@PathVariable注解，Spring MVC需要按名称找到匹配的URI模板变量。你可以在注解中指定它。
```java
@GetMapping("/owners/{ownerId}")
public String findOwner(@PathVariable("ownerId") String theOwner, Model model) {
    // 忽略实现
}
```
或者如果URI模板变量名称与方法参数名称匹配，则可以省略该详细信息。只要您的代码使用调试信息或Java 8上的-parameters编译器标记进行编译，Spring MVC会将方法参数雨URI模板变量名称进行匹配：
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
当在Map&lt;String，String>参数上使用@PathVariable注解时，map会被填充上所有URI模板变量。

URI模板可以从类型和方法级@RequestMapping注解中进行组合。因此，可以使用/owner/42/pets/21等URL调用findPet()方法。
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
@PathVariable参数可以是任何简单的类型，如int，long，Date等。Spring会自动转换为适当的类型或抛出TypeMismatchException异常,如果Spring转换失败的话。您还可以注册解析其他数据类型的支持。请参见[“方法参数和类型转换”](#方法参数和类型转换)一节以及[“自定义WebDataBinder初始化”](#自定义WebDataBinder初始化)一节。

### 使用正则表达式的URI模板模式
有时你需要更精确地定义URI模板变量。考虑URL“/spring-web/spring-web-3.0.5.jar”。你怎么把它分解成多个部分？

@RequestMapping注解支持在URI模板变量中使用正则表达式。语法是{varName:regex}，其中第一部分定义了变量名，第二部分定义了正则表达式。例如：
```java
@RequestMapping("/spring-web/{symbolicName:[a-z-]+}-{version:\\d\\.\\d\\.\\d}{extension:\\.[a-z]+}")
public void handle(@PathVariable String version, @PathVariable String extension) {
    // ...
}
```
### 路径模式
除了URI模板，@RequestMapping注解和所有的@RequestMapping变体还支持Ant风格的路径模式（例如/myPath/\*.do）。还支持URI模板变量和Ant-style glob的组合（例如/ owners / \* / pets / {petId}）。

### 路径模式比较
当一个URL匹配多个模式时，使用排序来查找最具体的匹配。

具有较低数量的URI变量和通配符的模式被认为更具体。例如/hotels/{hotel}/\*有一个URI变量和一个通配符，它被认为比有一个URI变量和两个通配符的/hotels/{hotel}/\*\*更具体。

如果两个模式有相同的数量，那么更长的那个被认为更具体。例如，/foo/bar\*比/foo/\*更长，因此也被认为更具体。

当两个模式有相同的数量和长度，那么有更少通配符的会被认为更具体。例如/hotels/{hotel}比/hotels/\*更具体。

还有一些额外的特殊规则：
- **默认的映射模式**/\*\* 比任何其他模式都更不具体。相比下/api/{a}/{b}/{c}更具体。
- **前缀模式**例如/public/\*\*比任何其他不包含双通配符的模式都更不具体。例如相比下/public/path3/{a}/{b}/{c}更具体。

有关详细信息，请参阅AntPathMatcher中的AntPatternComparator。注意，PathMatcher可以进行自定义（参见章节16.11“配置Spring MVC”中的[第16.11节“路径匹配”](#路径匹配)）。

### 有占位符的路径模式
@RequestMapping注解中的模式支持对本地属性和/或系统属性和环境变量的$ {...}占位符。在一个控制器需要映射的路径可能需要通过配置自定义的情况下，这很有用。关于占位符的更多信息，请参阅PropertyPlaceholderConfigurer类的javadocs 。

### 后缀模式匹配
默认情况下，Spring MVC执行“.\*”后缀模式匹配，以便映射到/person的控制器也隐式映射到/person.\*上。这使得通过URL路径可以轻松地请求资源的不同表示（例如/person.pdf，/person.xml）。

后缀模式匹配可以关闭或限制为一组明确注册用于内容协商的路径扩展。通常建议通过诸如/person/{id}之类的普通请求映射来减少歧义，这里其中的点可能不表示文件扩展名，例如/person/joe@email.com vs /person/joe@email.com.json。此外，如下面的注释中所说明的，后缀模式匹配以及内容协商可能在某些情况下被用于尝试恶意攻击，因此有充分的理由有意义地限制它们。

对于后缀模式匹配请参见[第16.11，“路径匹配”](#路径匹配),对于内容协商配置请参见[第16.6，“内容协商”](#内容协商)。

### 后缀模式匹配和RFD
Trustwave在2014年的论文中首次描述了反射文件下载 — Reflected file download（RFD）攻击。攻击类似于XSS，因为它依靠在响应中反映的输入（例如查询参数，URI变量）。然而，相比将JavaScript插入到HTML中，RFD攻击依赖浏览器切换为之行一个下载或者酱响应视为一个可执行脚本，如果如果根据文件扩展名双击（例如.bat，.cmd）的话。

在Spring MVC中@ResponseBody和ResponseEntity方法存在风险，因为它们可以呈现不同的内容类型，客户端可以通过URL路径拓展请求包含它们。但是请注意，禁用后缀模式匹配或禁用仅用于内容协商的路径扩展都可以有效地防止RFD攻击。

为了全面防范RFD，在渲染响应体之前，Spring MVC添加了一个Content-Disposition:inline;filename=f.txt头来建议一个固定和安全的下载文件文件名。但只有当URL路径包含一个既不在白名单中，也不是用于内容协商的目的而注册的文件扩展名，才会这么做。然而，当URL直接输入浏览器时，这可能会产生一个副作用。

许多常见的路径扩展名默认为白名单。此外，REST API调用通常在浏览器中不是直接用作URL。然而，使用自定义HttpMessageConverter实现的应用程序可以明确地注册用于内容协商的文件扩展名，并且不会为此类扩展添加Content-Disposition头。见[第16.6节“内容协商”](#内容协商)。

> 这是CVE-2015-5211工作的一部分。以下是报告中的其他建议：
- 编码而不是转义JSON响应。这也是OWASP XSS的建议。有关Spring的例子，请参阅[spring-jackson-owasp](#https://github.com/rwinch/spring-jackson-owasp)。
- 将后缀模式匹配配置为关闭或仅限于明确注册的后缀。
- 使用将属性“useJaf”和“ignoreUnknownPathExtensions”设置为false来配置内容协商，这会对带有未知拓展名的URL响应一个406错误。如果URL自然希望在最后有一个点的话，这可能不是一个选择。
- 添加X-Content-Type-Options: nosniff头到响应。 Spring Security 4默认情况下执行此操作。

### 矩阵变量
URI规范RFC 3986定义了在路径片段中包含name-value对的可能性。规范中没有使用特定的术语。一般的“URI路径参数”可能适用，尽管来自Tim Berners-Lee的旧帖子的更独特的“Matrix URI”也经常被使用并且是相当熟知的。在Spring MVC中，这些被称为矩阵变量（Matrix Variables）。

矩阵变量可以出现在任何路径段中，每个矩阵变量用“;”（分号）分隔。例如：“/cars;color=red;year=2012”。多个值可以是“,”（逗号）分隔“color=red,green,blue”，或者变量名称可以重复“color=red; color=green; color=blue”。

如果URL期望包含矩阵变量，则请求映射模式必须使用URI模板来表示它们。这确保了请求可以正确匹配，无论矩阵变量是否存在，以及它们以什么顺序提供。

以下是提取矩阵变量“q”的示例：
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
所有矩阵变量可以在Map中获得：
```java
// GET /owners/42;q=11;r=12/pets/21;q=22;s=23

@GetMapping("/owners/{ownerId}/pets/{petId}")
public void findPet(
        @MatrixVariable MultiValueMap<String, String> matrixVars,
        @MatrixVariable(pathVar="petId"") MultiValueMap<String, String> petMatrixVars) {

    // matrixVars: ["q" : [11,22], "r" : 12, "s" : 23]
    // petMatrixVars: ["q" : 11, "s" : 23]

}
```
请注意，为了使用矩阵变量，必须将RequestMappingHandlerMapping的removeSemicolonContent属性设置为false。默认设置为true。
> MVC Java配置和MVC命名空间都提供了使用矩阵变量的选项。
如果您使用Java配置，[使用MVC Java Config进行高级自定义](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#mvc-config-advanced-java)部分将介绍如何自定义RequestMappingHandlerMapping。    
在MVC命名空间中，&lt;mvc:annotation-driven>元素有enable-matrix-variables属性,它应该被设置为true。默认情况下它被设置为false。 
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
您可以通过指定Consumable Media Types的列表来缩小主要映射。只有当Content-Type请求头与指定的媒体类型匹配时，才会匹配该请求。例如：
```java
@PostMapping(path = "/pets", consumes = "application/json")
public void addPet(@RequestBody Pet pet, Model model) {
    // 忽略实现
}
```







