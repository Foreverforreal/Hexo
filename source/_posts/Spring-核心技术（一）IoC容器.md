title: Spring 核心技术（一）IoC容器
id: 1500988656165
author: 不识
tags:
  - Spring
categories:
  - Spring
date: 2017-07-25 21:17:00
---


[原文链接](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#beans)
***
# Spring IoC容器和bean介绍
***
本章涵盖了控制反转-*Inversion of Control*  （IoC）原理的Spring框架实现。IoC也被称为依赖注入-*dependency injection* （DI）。这是一个对象定义其依赖性的过程，，也就是它们所使用的其他对象，只能通过构造函数参数、工厂方法的参数，或者在对象实例构造或从工厂方法返回的对象实例中设置的属性。然后，容器在创建bean时注入这些依赖项。这个过程从根本上反转了bean本身通过直接构造类，或者使用一个如Service Locator模式的机制对依赖实例化或定位的控制，因此命名控制反转（IoC）。

org.springframework.beans和org.springframework.context包是Spring框架IoC容器的基础。BeanFactory接口提供了一个能够管理任何类型对象的高级配置机制。ApplicationContext是BeanFactory的一个子接口。它添加与Spring AOP功能的简单集成；消息资源处理（在国际化中使用），事件发布；以及应用层特定的上下文，例如用于Web应用程序的WebApplicationContext。

简而言之，BeanFactory提供配置框架和基本功能，ApplicationContext添加了更多的企业特定功能。ApplicationContext是BeanFactory的完整超集,本章仅用于Spring的IoC容器的描述。有关使用BeanFactory而不是ApplicationContext的更多信息，请参阅第7.16节“BeanFactory”。

在Spring中，构成应用程序主干并由Spring IoC容器管理的对象称为bean。bean是由Spring IoC容器实例化，组装和以其他方式管理的对象。不然，bean简单只是应用程序中的许多对象之一。Bean和它们之间的依赖关系反映在容器使用的配置元数据中。

***
# 容器概述
***
org.springframework.context.ApplicationContext接口表示Spring IoC容器，并且负责实例化，配置和组装上述的bean。容器通过读取配置元数据获取有关实例化，配置和组合的对象的说明。配置元数据用XML，Java注解或Java代码表示。它允许您表示组成应用程序的对象以及这些对象之间丰富的相互依赖关系。

ApplicationContext接口的几个实现与Spring一起提供。在独立应用程序中，通常创建一个ClassPathXmlApplicationContext或FileSystemXmlApplicationContext的实例。虽然XML已经称为定义配置元数据的传统格式，但你还可以指示容器使用Java注解或代码作为元数据格式，只需要通过提供少量的XML配置来声明开启对这些额外元数据格式的支持。

在大多数应用程序场景中，显式的用户代码不需要实例化一个或多个Spring IoC容器的实例。比如，在一个web应用程序情景中，在应用程序的web.xml文件中的一个简单的八行（或更多）的样板web描述XML通常就足够了（查看“Web应用程序的便捷ApplicationContext实例化”）。如果你使用Spring Tool Suite Eclipse-powered开发环境，则可以通过鼠标点击或击键轻松创建该样板设置。

下图是Spring如何工作的高级视图。您的应用程序类与配置元数据相结合，以便在创建和初始化ApplicationContext之后，您有一个完全配置和可执行的系统或应用程序。
![Spring IoC容器](/images/spring/container-magic.png.pagespeed.ce.-0JjaOG5As.png)
## 配置元数据
***
如前面的图标所展示，Spring IoC容器消耗一种形式的配置元数据；这个配置元数据表示你作为一个应用程序开发者如何告诉Spring容器去实例化，配置并且组装你程序中的对象。

配置元数据传统上由一个简单且直观的XML格式提供，这是本章用来传达Spring IoC容器的关键概念和特性大多数使用的方式。

>基于XML的元数据不是唯一允许的配置元数据形式。Spring IoC容器本身与实际写入此配置元数据的格式完全分离。这些天，很多开发人员为他们的Spring应用程序选择基于Java的配置。

有关使用Spring容器的其他形式的元数据的信息，请参阅：
- [基于注解的配置](#基于注解的容器配置)：Spring 2.5引入了基于注解的配置元数据的支持。
- [基于Java的配置](#基于Java的容器配置)：从Spring 3.0开始，Spring JavaConfig项目提供的许多功能成为核心Spring框架的一部分。因此，您可以使用Java而不是XML文件来定义应用程序类外部的bean。要使用这些新功能，请参阅@Configuration，@Bean，@Import和@DependsOn注解。

Spring配置由容器必须管理的至少一个和通常多个bean定义组成。基于XML的配置元数据显示在顶级&lt;beans/>元素内bean是以&lt;bean/>元素的形式配置。Java配置通常在@Configuration类中使用@Bean注解方法。

这些bean定义对应于构成应用程序的实际对象。通常，您定义服务层对象，数据访问对象（DAO），表示对象（如Struts Action实例），基础架构对象（如Hibernate SessionFactories，JMS Queues等）。通常，在容器中不配置细粒度的域对象，因为通常由DAO和业务逻辑来负责创建和加载这些域对象。但是，您可以使用Spring与AspectJ的集成来配置在IoC容器的控制之外创建的对象。请参阅[使用AspectJ对Spring进行依赖注入域对象]()。

以下示例显示了基于XML的配置元数据的基本结构：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="..." class="...">
        <!-- 这个bean的协作者和配置在这里 -->
    </bean>

    <bean id="..." class="...">
        <!-- 这个bean的协作者和配置在这里 -->
    </bean>

    <!-- 更多的bean定义到这里 -->

</beans>
```
id属性是用于标识单个bean定义的字符串。class属性定义bean的类型，并使用完全限定的类名。id属性的值指的是合作的对象。用于引用协作对象的XML在此示例中未显示;有关详细信息，请参阅[依赖]()。

## 实例化容器
***
实例化一个Spring IoC容器是直观的。提供给ApplicationContext构造函数的位置路径或路径实际上是资源字符串，这允许容器从各种外部资源（如本地文件系统），从Java CLASSPATH等中加载配置元数据。
```java
ApplicationContext context =
    new ClassPathXmlApplicationContext(new String[] {"services.xml", "daos.xml"});
```
> 在了解Spring的IoC容器之后，您可能想要了解更多有关Spring Resource抽象的信息，如“[资源]()”中所述，它提供了一种从URI语法中定义的位置读取InputStream的便捷机制。特别地，Resource路径用于构建应用程序上下文，如第8.7节“[应用程序上下文和资源路径]()”中所述。

下面示例展示了服务层对象（services.xml）配置文件：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- services -->

    <bean id="petStore" class="org.springframework.samples.jpetstore.services.PetStoreServiceImpl">
        <property name="accountDao" ref="accountDao"/>
        <property name="itemDao" ref="itemDao"/>
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <!-- service的更多bean定义到这里 -->

</beans>
```
下面示例展示了数据访问对象dao.xml文件：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="accountDao"
        class="org.springframework.samples.jpetstore.dao.jpa.JpaAccountDao">
        <!-- 这个bean的额外协作者和配置到这里 -->
    </bean>

    <bean id="itemDao" class="org.springframework.samples.jpetstore.dao.jpa.JpaItemDao">
        <!-- 这个bean的额外协作者和配置到这里 -->
    </bean>

    <!-- 数据访问对象的更多bean定义到这里 -->

</beans>
```
在前面的示例中，服务层由类PetStoreServiceImpl和两个数据访问对象类型为JpaAccountDao和JpaItemDao（基于JPA对象/关系映射标准）组成。property name元素是指JavaBean属性的名称，而ref元素是指另一个bean定义的名称。id和ref元素之间的这种链接表示协作对象之间的依赖关系。有关配置对象的依赖关系的详细信息，请参阅[依赖]()。

**编写基于XML的配置元数据**