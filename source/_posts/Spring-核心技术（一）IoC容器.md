title: Spring 核心技术（一）IoC容器
id: 1500988656165
author: 不识
tags:
  - Spring
categories:
  - Spring
date: 2017-07-25 21:17:00
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

[原文链接](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#beans)
***
# Spring IoC容器和bean介绍
***
本章涵盖了控制反转-*Inversion of Control*  （IoC）原理的Spring框架实现。IoC也被称为依赖注入-*dependency injection* （DI）。它是一个通过对象定义它们依赖的过程，也就是它们使用的其他对象，这些依赖只能通过构造函数参数、工厂方法的参数，或者已经被构建或由工厂方法返回的对象实例上设置的属性这些形式。。然后，容器在创建bean时注入这些依赖项。这个过程从根本上反转了bean本身通过直接构造类，或者使用一个如Service Locator模式的机制对依赖实例化或定位的控制，因此命名控制反转（IoC）。
<!-- more -->
**org.springframework.beans** 和 **org.springframework.context** 包是Spring框架IoC容器的基础。BeanFactory接口提供了一个能够管理任何类型对象的高级配置机制。ApplicationContext是BeanFactory的一个子接口。它添加与Spring AOP功能的简单集成；消息资源处理（在国际化中使用），事件发布；以及应用层特定的上下文，例如用于Web应用程序的WebApplicationContext。

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

### 编写基于XML的配置元数据
让bean定义跨越多个XML文件是有用的。每个单独的XML配置文件通常表示您的体系结构中的逻辑层或模块。

你可以使用ApplicationContext构造函数从所有这些XML片段中加载bean定义。如上一节所示那样，构造函数接受多个**Resource**位置。或者，使用一个或多个出现的<import />元素从另一个文件加载bean定义。例如：
```xml
<beans>
    <import resource="services.xml"/>
    <import resource="resources/messageSource.xml"/>
    <import resource="/resources/themeSource.xml"/>

    <bean id="bean1" class="..."/>
    <bean id="bean2" class="..."/>
</beans>
```
在上述示例中，外部定义的bean从三个文件中加载：services.xml，messageSource.xml和themeSource.xml。所有的位置路径是相对于做导入操作的定义文件，所以services.xml必须在与导入的文件相同的目录或类路径位置，而messageSource.xml和themeSource.xml必须位于导入文件位置下的resources位置中。<font color= "#1469b3">如你看到的，一个前置的斜杠被忽略，但是鉴于这些路径是相对的，所以最好不要使用斜杠</font>。被导入文件的内容包括顶级&lt;beans />元素,它必须是根据Spring Schema有效的XML bean定义文件。

> 这有可能但是不推荐，使用一个相对"../"路径引用上级目录的文件。这样做会创建对当前应用程序之外的文件的依赖。特别是，这个引用不推荐用于“classpath：”URL（例如“classpath：../ services.xml”），运行时解析过程选择“最近”类路径根，然后查看其父目录。Classpath配置的更改可能会导致选择一个不同的，不正确的目录。     
你可以一直使用完全限定的资源位置而不是性对路径：比如， "file:C:/config/services.xml" 或 "classpath:/config/services.xml"。然而，请注意，您将应用程序的配置与特定的绝对位置相耦合在一起。通常最好与这些绝对路径保持一个间接关联，比如，通过 "${…}" 占位符在运行时期解析JVM系统属性

import指令是由beans命名空间本身提供的一个功能。Spring提供的一些XML命名空间中可以使用除纯bean定义之外的更多配置功能，例如。 “context”和“util”命名空间。

### Groovy Bean定义DSL
作为外部配置元数据的另一个例子，bean定义也可以在Spring的Groovy Bean Definition DSL中表示，如Grails框架所知。通常，这种配置将存在一个“.groovy”文件中，结构如下：
```groovy
beans {
    dataSource(BasicDataSource) {
        driverClassName = "org.hsqldb.jdbcDriver"
        url = "jdbc:hsqldb:mem:grailsDB"
        username = "sa"
        password = ""
        settings = [mynew:"setting"]
    }
    sessionFactory(SessionFactory) {
        dataSource = dataSource
    }
    myService(MyService) {
        nestedBean = { AnotherBean bean ->
            dataSource = dataSource
        }
    }
}
```
这种配置风格在很大程度上等同于XML bean定义，甚至支持Spring的XML配置命名空间。它还允许通过“importBeans”指令导入XML bean定义文件。

## 使用容器
***
**ApplicationContext**是能够维护不同bean和它们的依赖关系的注册表的高级工厂的接口。使用**T getBean(String name, Class&lt;T> requiredType)**方法你可以检索你的bean实例。

**ApplicationContext**使您能够读取bean定义并访问它们，如下所示：
```java
// 创建并配置bean
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");

// 获取配置实例
PetStoreService service = context.getBean("petStore", PetStoreService.class);

// 使用配置实例
List<String> userList = service.getUsernameList();
```
使用Groovy配置，引导看起来非常相似，只是一个不同的上下文实现类，它是Groovy感知的（但也可以理解XML bean定义）：
```java
ApplicationContext context = new GenericGroovyApplicationContext("services.groovy", "daos.groovy");
```
最灵活的变体是GenericApplicationContext与reader委托组合，例如与XML文件的XmlBeanDefinitionReader：
```java
GenericApplicationContext context = new GenericApplicationContext();
new XmlBeanDefinitionReader(context).loadBeanDefinitions("services.xml", "daos.xml");
context.refresh();
```
或者为Groovy文件使用GroovyBeanDefinitionReader 
```java
GenericApplicationContext context = new GenericApplicationContext();
new GroovyBeanDefinitionReader(context).loadBeanDefinitions("services.groovy", "daos.groovy");
context.refresh();
```
这样的reader代理可以在相同的ApplicationContext中进行混合和匹配，从不同的配置源读取bean定义，如果需要的话。

然后你可以使用getBean来获取你的bean实例。AApplicationContext接口还有其他一些获取bean的方法，但理想情况下你的应用程序代码应该从不会使用到它们。实际上，您的应用程序代码根本不应该调用getBean()方法，因此根本不需要依赖Spring API。例如，Spring与Web框架的集成为各种Web框架组件（如controller和JSF-managed bean）提供依赖注入，允许你在指定bean上通过元数据声明一个依赖（例如自动装配注解）

***
# Bean概述
***
Spring IoC容器管理一个或多个bean。这些bean是使用您提供给容器的配置元数据创建的，例如，以XML <bean />定义的形式。

在容器本身内，这些bean定义以BeanDefinition对象表示，它包含了以下元数据（以及其他信息）：

- 一个包完全限定类名称：通常是要定义的bean的实际实现类。
- Bean行为配置元素，它说明bean应该在容器（作用域，生命周期回调等）中的行为。
- 这个bean工作所需引用的其他bean；这些引用也被称为协作者或依赖。
- 在新创建的对象中设置其他配置设置，例如，在管理连接池的bean中使用的连接数，或池的大小限制。

该元数据转换为组成每个bean定义的一组属性。

|属性|解释...|
|----|-------|
|class|3.2[“实例化bean”](#实例化bean)
|name|3.1[“命名bean”](#命名bean)
|scope|5 [“Bean域”](#Bean域)
|constructor arguments|4.1[“依赖注入”](#依赖注入)
|properties|3.1[“依赖注入”](#依赖注入)
|autowiring mode|4.5[“自动装配协作者”](#自动装配协作者)
|lazy-initialization mode|4.4[“懒加载bean”](#懒加载bean)
|initialization method|the section called “Initialization callbacks”
|destruction method|the section called “Destruction callbacks”

除了包含有关如何创建特定bean的信息的bean定义之外，ApplicationContext的实现还允许用户注册在容器外创建的现有对象。这是通过访问ApplicationContext的getBeanFactory()方法获取BeanFatory，这个方法返回BeanFactory的实现DefaultListableBeanFactory。DefaultListableBeanFactory通过registerSingleton(..)和 registerBeanDefinition(..)方法支持这个注册。然而，通常应用程序仅使用通过元数据bean定义定义的bean。

> Bean元数据和手动提供的单例实例需要尽早注册，以便容器在自动装配和其他内省步骤期间正确地对其进行推理。然而在某种程度上也支持覆盖已有的元数据和单例实例，在运行时期新的bean注册（同时实时访问工厂）是不被正式支持的，并且可能导致bean容器中的并发访问异常和/或不一致的状态。

## 命名bean
***
每个bean都有一个或多个标识符。这些标识符在托管该bean的容器中必须是唯一的。一个bean通常只有一个标识符，但是如果它需要不止一个的话，额外的可以被认为是别名。

在基于XML配置元数据，你使用id和/或name属性来指定bean的标识符。id属性允许你指定一个id。通常这些名称是字母数字（'myBean'，'fooService'等），但也可能包含特殊的字符。如果你想要向bean引入其他别名，你可以在name属性中指定它们，并用逗号（，），分号（;）或空白来分隔。作为一个历史记录，在Spring 3.1之前的版本中，id属性被定义为一个xsd:ID类型，它限制了可能的字符。从3.1开始，它被定义为一个xsd:string类型。注意容器依然强制bean id的唯一性，尽管不再是XML解析。

您不需要为bean提供名称或ID。如果没有明确提供名称或ID，容器将为该bean生成唯一的名称。但是，如果要通过名称引用该bean，通过使用ref元素或 Service Locator样式查找，你必须提供一个名称。不提供名称的意图与使用 [内部bean](#内部bean)和自动装配依赖有关。

<div style="border: 1px solid #ccc;background-color:#f8f8f8;padding:20px;">**bean命名惯例**   
命名bean时的惯例是使用标准Java用于实例字段命名的惯例。也就是，bean名称以一个小写字母开始，后面是是驼峰式命名。这样命名的示例是（不带引号）"accountManager"，"accountService"，"userDao"等等。      
一致的bean命名使您的配置更容易阅读和理解，并且如果你使用Spring AOP ，在通过名称将advice应用到到一组bean时，这会非常有帮助。
</div> 

> 在类路径中通过组件扫描，Spring会按照上述规则为未命名组件生成bean名称：实质上是采用简单类名称，并将它的第一个字母转换为小写。然而，在（异常）特殊情况下，如果有多个字符，并且第一个和第二个字符都是大写字母，则原始大写将被保留。这些与java.beans.Introspector.decapitalize（也是这里Spring使用的）定义的规则相同。


### 在bean定义外为bean起别名      
在bean定义本身中，您可以通过使用由id属性指定的最多一个名称和在name属性中任意数量的其他名称，组合来为bean提供多个名称。这些名称可以是同一个bean的等价别名，并且在一些情况下有用，例如允许应用程序中的每个组件通过使用指定给该组件本身的bean名称来引用一个公共依赖。

但是在bean实际定义的地方指定它的所有别名并不是总是足够的。有时需要为一个在其他地方定义的bean引入一个别名。这在一个大型系统中通常是这样，这样的系统它的配置被分隔在每个子系统中，每一个子系统都有它自己的一系列对象定义。在基于XML的配置元数据中，你可以使用&lt;alias>元素来完成此操作。
```xml
<alias name="fromName" alias="toName"/>
```
在这个情景中，在相同容器中一个名称为fromName的bean，也可能在使用这个别名定义后，可以以toName引用。

例如，子系统A的配置元数据可能通过名称subsystemA-dataSource引用一个DateSource。子系统B的配置元数据可能通过名称subsystemB-dataSource引用一个DateSource。当使用这两个子系统构成主程序，主程序通过名称myApp-dataSource引用这个DataSource。要使所有三个名称都引用相同的对象，你向MyApp配置元数据中添加以下别名定义：
```xml
<alias name="subsystemA-dataSource" alias="subsystemB-dataSource"/>
<alias name="subsystemA-dataSource" alias="myApp-dataSource" />
```
现在，每个组件和主应用程序可以通过唯一的名称来引用dataSource，并保证不会与任何其他定义（有效创建命名空间）冲突，但它们引用同一个bean。

## 实例化bean
***
bean定义本质上是创建一个或多个对象的配方。当询问时，容器会查看命名bean的配方，并使用由该bean定义封装的配置元数据创建（或获取）实际对象。

如果使用基于XML的配置元数据，则可以在&lt;bean/>元素的class属性中指定要实例化的对象的类型（或类）。class属性（attribute），在内部是BeanDefinition实例上的Class属性（property），通常是必须的。您可以通过以下两种方式之一使用Class属性：
- 通常，在容器本身通过反射调用它的构造器直接创建bean的情况下，指定被构建的bean类，这与Java代码使用new操作符有些等同。
- 要指定一个实际的类，它包含了会被调用来创建对象的静态工厂方法，在这种不常见的情况下，容器会在一个类上调用静态工厂方法来创建一个bean。从调用静态工厂方法返回的对象类型可能完全是同一个类或另一个类。

<div style="border: 1px solid #ccc;background-color:#f8f8f8;padding:20px;">**内部类名称** 如果你校内概要为一个静态嵌套类配置一个bean定义，你必须使用嵌套类的二进制名称。    
比如，如果你在com.example包有一个类叫Foo，并且这个Foo类有一个静态嵌套类叫Bar，那么在bean定义上'class'的值会是...    
**com.example.Foo$Bar**
注意，在名称中使用$字符将外部类名称与嵌套类名称分隔开。
</div>

### 使用构造函数实例化
当通过构造函数方法创建一个bean时，所有普通类都可以使用并与Spring兼容。也就是说，正在开发的类不需要实现任何特定的接口或以特定方式编码。只需指定bean类即可。然而，根据为这个指定bean所使用的IoC类型，你可能需要一个默认（空）的构造函数。

Spring IoC容器几乎可以管理您想要管理的任何类;它不限于只管理真正的JavaBeans。大多数Spring用户喜欢实际的JavaBeans，它只有一个默认（无参）构造函数和在容器中属性之后仿照的适当的setter和getter。您还可以在容器中拥有更多异国情调的非Bean风格的类。例如，如果您需要使用绝对不遵守JavaBean规范的旧版连接池，那么Spring也可以管理它。

使用基于XML配置元数据，你可以如下指定你的bean类：
```xml
<bean id="exampleBean" class="examples.ExampleBean"/>

<bean name="anotherExample" class="examples.ExampleBeanTwo"/>
```
关于为构造函数提供参数（如果需要的话）和在对象构建后设置对象实例属性的机制更多的信息，参阅[“注入依赖”](#注入依赖)

### 使用静态工厂方法实例化
当你使用一个静态工厂方法创建一个bean定义，你使用class属性来指定包含这个静态工厂方法的类，并且使用一个名为factory-method的属性指定这个工厂方法本身。您应该可以调用此方法（随后可以使用可选参数），并返回一个活动对象，随后将其视为通过构造函数创建的对象。这种bean定义的一个用途是在旧代码中调用静态工厂。

下面的bean定义指定这个bean会被通过调用一个工厂方法来创建。这个定义没有指定返回对象的类型（类），只指定了包含此工厂方法的类。在这个例子中，createInstance()方法必须是静态方法。
```xml
<bean id="clientService"
    class="examples.ClientService"
    factory-method="createInstance"/>
```
```java
public class ClientService {
    private static ClientService clientService = new ClientService();
    private ClientService() {}

    public static ClientService createInstance() {
        return clientService;
    }
}
```
有关为工厂方法提供（可选）参数和从工厂返回对象后，设置对象实例属性机制的更多信息，参阅[“依赖和配置细节”]()

### 使用实例工厂方法实例化
类似于通过静态工厂方法实例化，也可以使用一个实例工厂方法，调用容器中现有的bean的非静态方法来创建一个新的bean。要使用这种机制，让class属性为空，并且在 **factory-bean** 属性中指定当前（或父/祖）容器中包含了要用来调用用于创建对象的实例方法的bean名称。使用 **factory-method** 属性设置工厂方法本身名称。
```xml
<!-- 工厂bean，其中包含一个名为createInstance()的方法， -->
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
    <!-- 注入此定位器bean所需的任何依赖项 -->
</bean>

<!-- 要通过工厂bean创建的bean -->
<bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/>
```
```java
public class DefaultServiceLocator {

    private static ClientService clientService = new ClientServiceImpl();
    private DefaultServiceLocator() {}

    public ClientService createClientServiceInstance() {
        return clientService;
    }
}
```
一个工厂类也可以有多个工厂方法，如下所示：
```xml
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
    <!-- 注入此定位器bean所需的任何依赖项 -->
</bean>

<bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/>

<bean id="accountService"
    factory-bean="serviceLocator"
    factory-method="createAccountServiceInstance"/>
```
```java
public class DefaultServiceLocator {

    private static ClientService clientService = new ClientServiceImpl();
    private static AccountService accountService = new AccountServiceImpl();

    private DefaultServiceLocator() {}

    public ClientService createClientServiceInstance() {
        return clientService;
    }

    public AccountService createAccountServiceInstance() {
        return accountService;
    }

}
```
这种方法表明，工厂bean本身可以通过依赖注入（DI）进行管理和配置。详细信息请参见[“依赖和配置细节”]()。

> 在Spring文档中，工厂bean（factory bean）是指在Spring容器中配置，通过实例或静态工厂方法创建对象的bean。相比之下，FactoryBean（注意大写）是指一个Spring特定的FactoryBean。

***
# 依赖
***
一个典型的企业应用程序不是由单个对象（或在Spring说法中是bean）组成的。即使是最简单的应用程序，也有几个对象共同合作，来展示最终用户看到的整体的应用程序。下节解释你如何定义一系列独立于一个完全实现的应用程序的bean定义，这样的应用程序对象互相协作完成一个目标。

## 依赖注入
***
依赖注入（DI）是一个通过对象定义它们依赖的过程，也就是它们使用的其他对象，这些依赖只能通过构造函数参数、工厂方法的参数，或者已经被构建或由工厂方法返回的对象实例上设置的属性这些形式。然后，容器在创建bean时注入这些依赖项。这个过程从根本上反转了bean本身通过直接构造类，或者使用一个如Service Locator模式的机制对依赖实例化或定位的控制，因此命名控制反转（IoC）。

使用DI原理，代码更加清晰，并且当提供对象依赖的时候，解耦更加有效。对象不查找其依赖关系，并且不知道依赖的位置或类。因此，您的类变得更容易测试，特别是当依赖在接口或抽象基类上时，它允许在单元测试中使用存根或模拟实现。

DI存在两种主要的变体：[基于构造函数的依赖注入](#基于构造函数的依赖注入) 和 [基于Setter的依赖注入](#基于Setter的依赖注入)。

### 基于构造函数的依赖注入
基于构造函数的DI通过容器调用具有多个参数的构造函数完成，每个参数表示一个依赖。调用具有特定参数的静态工厂方法来构造bean与之几乎相当，并且这个讨论类似地处理构造函数和静态工厂方法的参数。以下示例显示一个只能使用构造函数注入的依赖注入的类。请注意，这个类没有什么特别之处，它是一个POJO，它不依赖于容器特定的接口，基类或注解。
```java
public class SimpleMovieLister {

    // SimpleMovieLister对MovieFinder有依赖关系
    private MovieFinder movieFinder;

    // 一个构造函数，使Spring容器可以注入一个MovieFinder
    public SimpleMovieLister(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // 省略实际使用注入的MovieFinder的业务逻辑

}
```

**构造函数参数解析**   
使用参数的类型进行构造函数参数解析匹配。如果在bean定义的构造函数参数中不存在潜在的歧义，那么构造函数参数在bean定义中定义的顺序就是在bean被实例化时将这些参数提供给适当的构造函数的顺序。考虑以下类：
```java
package x.y;

public class Foo {

    public Foo(Bar bar, Baz baz) {
        // ...
    }

}
```
没有潜在的歧义存在，假设Bar和Baz类没有继承关系。因此，以下配置工作正常，您不需要在<constructor-arg />元素中显式指定构造函数参数索引和/或类型。
```xml
<beans>
    <bean id="foo" class="x.y.Foo">
        <constructor-arg ref="bar"/>
        <constructor-arg ref="baz"/>
    </bean>

    <bean id="bar" class="x.y.Bar"/>

    <bean id="baz" class="x.y.Baz"/>
</beans>
```
当引用另一个bean时，类型是已知的，并且可以进行匹配（如前面的例子所示）。当使用一个简单类型，例如&lt;value>true&lt;/value>时，在没有帮助的情况下Spring无法确定值的类型，因此无法通过类型匹配。考虑以下类：
```java
package examples;

public class ExampleBean {

    // 计算终极答案的年数
    private int years;

    // 生命，宇宙及一切的答案（银河漫游指南的梗）
    private String ultimateAnswer;

    public ExampleBean(int years, String ultimateAnswer) {
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }

}
```
在上述情况下，如果使用type属性显式指定构造函数参数的类型，则容器可以用简单类型执行类型匹配。例如：
```xml
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg type="int" value="7500000"/>
    <constructor-arg type="java.lang.String" value="42"/>
</bean>
```
使用index属性来明确指定构造函数参数的索引。例如：
```xml
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg index="0" value="7500000"/>
    <constructor-arg index="1" value="42"/>
</bean>
```
除了解决多个简单值的歧义之外，指定索引可以解决构造函数具有两个相同类型参数的歧义。注意，索引从0开始。

您还可以使用构造函数参数名称为值消除歧义：
```xml
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg name="years" value="7500000"/>
    <constructor-arg name="ultimateAnswer" value="42"/>
</bean>
```
请记住，为了使这项工作开箱即用，你的代码必须在启用调试标志的情况下进行编译，以便Spring可以从构造函数中查找参数名称。如果您无法使用调试标志（或不想）编译代码，则可以使用@ConstructorProperties JDK注解来明确命名构造函数参数。示例类必须如下所示：
```java
package examples;

public class ExampleBean {

    // 忽略字段
    
    @ConstructorProperties({"years", "ultimateAnswer"})
    public ExampleBean(int years, String ultimateAnswer) {
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }

}
```

### 基于Setter的依赖注入
基于Setter的DI通过在调用无参数构造函数或无参数静态工厂方法来实例化bean之后，通过容器调用bean的setter方法来实现。

以下示例显示了一个类，它只能使用纯setter注入进行依赖注入。这个类是常规Java。它是一个POJO，它不依赖容器特定的接口，基类或注解。
```java
public class SimpleMovieLister {

    // SimpleMovieLister依赖MovieFinder
    private MovieFinder movieFinder;

    // 一个setter方法，使Spring容器可以注入一个MovieFinder
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // 省略实际使用注入的MovieFinder的业务逻辑

}
```
**ApplicationContext** 对于它管理的bean支持基于构造函数和基于setter的DI。在一些依赖已经通过构造函数方式注入后，它也支持基于setter的DI。您可以以BeanDefinition的形式配置依赖，它与PropertyEditor实例结合使用，以将属性从一种格式转换为另一种格式。然而，大多数Spring用户不直接使用这些类（即以编程方式），而是使用XML bean定义，注解组件（即，使用@Component，@Controller等注解的类）或者在基于Java@Configuration类中的@Bean方法。然后将这些资源内部地转换为BeanDefinition的实例，并用于加载整个Spring IoC容器实例。

<div style="border: 1px solid #ccc;background-color:#f8f8f8;padding:20px;">**基于构造还是基于setter DI**      
由于可以混合使用基于构造函数和基于setter的DI，为强制依赖使用构造函数，并且为可选依赖使用setter方法或配置方法是一个很好的经验法则。请注意，可以在setter方法上使用的的@Required注解可以使属性成为必需的依赖。<br> 
Spring团队通常主张构造函数注入，因为它可以实现应用程序组件为不可变对象，并确保所需的依赖关系不为null。此外，构造函数注入的组件总是以完全初始化的状态返回给客户端（调用）代码。作为一个附注，大量的构造函数参数是一个不好的代码气息，这意味着该类可能有太多的责任，应该重构以更好地解决问题的正确分离。<br>
Setter注入应主要用于可选依赖，可以在类中分配合理的默认值。否则，必须在代码任何使用依赖的地方执行非空检查。setter注入的一个好处是setter方法使得该类的对象可以在以后重新配置或重新注入。因此对于setter注入，通过 JMX MBeans管理是一个引人注目的用例。<br> 
使用对特定类最有意义的DI风格。有时候，当您处理没有来源的第三方类时，您来做选择。例如，如果第三方类不暴露任何setter方法，那么构造函数注入可能是DI的唯一可用形式。     
</div>

### 依赖解析过程
容器如下执行bean依赖解析：
- **ApplicationContext**由描述所有bean的配置元数据创建和初始化。配置元数据可以通过XML，Java代码或注解来指定。
- 对于每一个bean，它的依赖以属性，构造函数参数或静态工厂方法参数（如果你使用它来替代一个正常的构造函数）形式表现。当bean实际创建时，这些依赖被提供给bean。
- 每个属性或构造函数参数是要设置的值的实际定义，或对容器中另一个bean的引用。
- 作为值的每个属性或构造函数参数都将从其指定的格式转换为该属性或构造函数参数的实际类型。默认情况下，Spring可以将字符串格式提供的值转换为所有内置类型，如int，long，String，boolean等。

Spring容器会在容器创建时验证每个bean的配置。但是，在bean实际创建之前，不会设置bean的属性。在容器创建时，单域和被设置为预实例化（默认设置）的bean会被创建。域在[“Bean域”](#Bean域)这一章定义。否则的话，bean只在被需要的时候才会创建。bean的创建可能会导致一个bean树被创建，因为bean的依赖和它依赖的依赖（等等）被创建和分配。注意这些依赖间的解析不匹配可能之后才会出现，也就是在首次创建受影响的bean的时候。


<div style="border: 1px solid #ccc;background-color:#f8f8f8;padding:20px;">**循环依赖** 如果你主要使用构造函数注入，可能会创建一个无法解决的循环依赖场景。<br> <br> 例如：类A通过构造函数依赖需要一个类B的实例，而类B通过构造函数注入又需要类A的实例。如果你为类A和B配置bean为互相注入，Spring IoC容器在运行时期探测到这个循环引用，就会抛出一个BeanCurrentlyInCreationException。<br> <br> 一个可能的解决方案是编辑这些类的源码，将其通过setter而不是构造函数配置。或者，避免构造函数注入而只使用setter注入。换而言之，你可以使用setter注入配置循环依赖，尽管这是不被推荐的。<br> <br>不像通常的情况（没有循环依赖），bean A和bean B之间的循环依赖关系强制其中一个bean在被完全初始化之前被注入到另一个bean中（一个经典的鸡/鸡蛋场景）。
</div>

你一般可以信任Spring做了正确的事情。它在容器加载时检测到配置问题，例如对不存在的bean的引用和循环依赖。当bean被创建后，Spring尽可能晚的设置属性和解析依赖。这意味着Spring容器被正确加载，但稍后你请求一个对象，如果在创建对象或者创建一个它的依赖上存在问题，容器会抛出一个异常。例如，bean由于缺少或无效的属性而抛出一个异常。这可能延迟了一些配置问题的发现，这也是为什么ApplicationContext的实现默认预实例化单例bean。以一些前期的时间和内存为代价，在bean实际需要前就创建它们，这样你可以在ApplicationContext创建时就发现配置的问题，而不是之后。你依旧可以重写这个默认行为，使单例bean懒加载，而不是预实例化。

如果没有循环依赖存在，当一个或多个协作bean被注入到一个需要该依赖的bean中时，每个协作bean在被注入到需要该依赖的bean之前都会被完全配置。这意味着如果beana A依赖bean B，Spring IoC容器在调用bean A的setter方法之前已经完全配置了bean B。换而言之，bean被实例化（如果不是一个预实例化单例），它的依赖被设置完成，并且相应的生命周期方法（比如 [配置的init方法]() 或 [初始化bean回调方法]()  ）已被调用。

### 依赖注入示例
以下示例为基于setter的DI使用基于XML的配置元数据。Spring XML配置文件的一小部分指定了一些bean定义：
```xml
<bean id="exampleBean" class="examples.ExampleBean">
    <!-- 使用了嵌套ref元素的setter注入 -->
    <property name="beanOne">
        <ref bean="anotherExampleBean"/>
    </property>

    <!-- 使用更加整洁的ref属性的setter注入 -->
    <property name="beanTwo" ref="yetAnotherBean"/>
    <property name="integerProperty" value="1"/>
</bean>

<bean id="anotherExampleBean" class="examples.AnotherBean"/>
<bean id="yetAnotherBean" class="examples.YetAnotherBean"/>
```
```java
public class ExampleBean {

    private AnotherBean beanOne;
    private YetAnotherBean beanTwo;
    private int i;

    public void setBeanOne(AnotherBean beanOne) {
        this.beanOne = beanOne;
    }

    public void setBeanTwo(YetAnotherBean beanTwo) {
        this.beanTwo = beanTwo;
    }

    public void setIntegerProperty(int i) {
        this.i = i;
    }

}
```
在上述示例中，setter被声明与XML文件中指定的属性相匹配。以下示例使用基于构造函数的DI：
```xml
<bean id="exampleBean" class="examples.ExampleBean">
    <!-- 使用嵌套的ref元素的构造函数注入 -->
    <constructor-arg>
        <ref bean="anotherExampleBean"/>
    </constructor-arg>

    <!-- 使用更加整洁的ref属性的构造函数注入 -->
    <constructor-arg ref="yetAnotherBean"/>

    <constructor-arg type="int" value="1"/>
</bean>

<bean id="anotherExampleBean" class="examples.AnotherBean"/>
<bean id="yetAnotherBean" class="examples.YetAnotherBean"/>
```
```java
public class ExampleBean {

    private AnotherBean beanOne;
    private YetAnotherBean beanTwo;
    private int i;

    public ExampleBean(
        AnotherBean anotherBean, YetAnotherBean yetAnotherBean, int i) {
        this.beanOne = anotherBean;
        this.beanTwo = yetAnotherBean;
        this.i = i;
    }

}
```
在bean定义中指定的构造函数参数将被用作ExampleBean的构造函数的参数。

现在考虑这个例子的一种变体，不再使用构造函数，而是告诉Spring调用一个静态方法来返回这个对象的一个实例：
```xml
<bean id="exampleBean" class="examples.ExampleBean" factory-method="createInstance">
    <constructor-arg ref="anotherExampleBean"/>
    <constructor-arg ref="yetAnotherBean"/>
    <constructor-arg value="1"/>
</bean>

<bean id="anotherExampleBean" class="examples.AnotherBean"/>
<bean id="yetAnotherBean" class="examples.YetAnotherBean"/>
```
```java
public class ExampleBean {

    // 一个私有构造器
    private ExampleBean(...) {
        ...
    }

    // 一个静态工厂方法; 这个方法的参数可以是该方法返回bean
    //的依赖，不管这些参数是否实际被使用
    public static ExampleBean createInstance (
        AnotherBean anotherBean, YetAnotherBean yetAnotherBean, int i) {

        ExampleBean eb = new ExampleBean (...);
        //一些其他操作...
        return eb;
    }

}
```
静态工厂方法的参数通过&lt;constructor-arg/>元素提供，与实际构造函数使用的完全相同。这个工厂方法返回的类的类型不必和包含该静态工厂方法的类的类型一样，尽管在这个实例中是一样的。一个实例（非静态）工厂方法将以基本相同的方式使用（除了使用factory-bean属性而不是class属性），所以这里不再赘述。

## 依赖和配置细节
***
如在前面章节中提到的，你可以将bean属性和构造函数参数定义为对其他被管理的bean（协作者），或行内定义值的引用。为这个目的，Spring基于XML配置元数据在它的&lt;property />和&lt;constructor-arg />元素中支持子元素类型。

### 直接值（原始类型，String等等）
&lt;property />元素的value属性将一个属性或者构造函数参数指定为人类可读的字符串表示形式。Spring的 [转换服务用]() 于将这些值从String转换为实际的属性或参数类型。
```xml
<bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
    <!-- 导致一个setDriverClassName(String)调用 -->
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/mydb"/>
    <property name="username" value="root"/>
    <property name="password" value="masterkaoli"/>
</bean>
```
以下示例使用 [p命名空间](#使用p命名空间的XML快捷方式) 进行更简洁的XML配置。
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource"
        destroy-method="close"
        p:driverClassName="com.mysql.jdbc.Driver"
        p:url="jdbc:mysql://localhost:3306/mydb"
        p:username="root"
        p:password="masterkaoli"/>

</beans>
```
上面的XML更简洁;然而，在运行时而不是设计时发现错字，除非您在创建bean定义时使用支持自动属性完成的IDE（如IntelliJ IDEA或Spring Tool Suite（STS））。

你也可以如下配置一个java.util.Properties实例：
```xml
<bean id="mappings"
    class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">

    <!-- 以java.util.Properties类型 -->
    <property name="properties">
        <value>
            jdbc.driver.className=com.mysql.jdbc.Driver
            jdbc.url=jdbc:mysql://localhost:3306/mydb
        </value>
    </property>
</bean>
```
Spring容器通过使用JavaBeans PropertyEditor机制将&lt;value/>元素内的文本转换为java.util.Properties实例。这是一个很好的捷径，而且是Spring团队赞成在value属性样式中使用嵌套的&lt;value/>元素的几个地方之一。

#### idref元素
idref元素只是将容器中另一个bean的id（字符串值 - 不是引用）传递给&lt;constructor-arg/>或&lt;property/>元素的错误方法。
```xml
<bean id="theTargetBean" class="..."/>

<bean id="theClientBean" class="...">
    <property name="targetName">
        <idref bean="theTargetBean"/>
    </property>
</bean>
```
上述bean定义片段与以下代码片段完全相同（在运行时）：
```xml
<bean id="theTargetBean" class="..." />

<bean id="client" class="...">
    <property name="targetName" value="theTargetBean"/>
</bean>
```
第一种形式优于第二种形式，因为使用idref标签允许容器在部署时验证所引用的，命名的bean实际存在。在第二个变体中，不会对传递给client bean的targetName属性的值执行验证。只有当client bean被实际实例化的时候，错别字才会被发现（最可能是致命的结果）。如果client bean是一个 [原型]() bean,这种错字和它产生的异常只能在容器部署后很久才能被发现。
> 在4.0 beans xsd中，idref元素上的local属性不再受支持，因为它不再提供超过常规bean引用的值。在升级到4.0 schema时，只需将您现有的idref local引用更改为idref bean。

&lt;idref/>元素带来值一个常用的地方是在ProxyFactoryBean bean定义中AOP拦截器的配置中（至少早在Spring 2.0版本）。指定拦截器名称时使用&lt;idref/>元素可防止拼写错误。

### 引用其他bean（协作者）
ref元素是&lt;constructor-arg/>或&lt;property/>定义元素中的最后一个元素。在这里，您可以将bean的指定属性的值设置为对由容器管理的另一个bean（协作者）的引用。这个被引用的bean是进行属性设置bean的依赖，并且他在属性设置前被引用bean一经请求就进行初始化（如果协作者是单例bean，它可能已经被容器初始化。）。所有引用最终都是对另一个对象的引用。范围和验证取决于你是否通过 bean, local,或parent属性指定其他对象的id/name。

通过&lt;ref/>标签的bean属性指定目标bean是最常用的形式，并允许创建对在同一容器或父容器中任何bean的引用，而不管它是否在同一个XML文件中。bean属性的值可能与目标bean的id属性相同，或者与目标bean的name属性的一个值相同。
```xml
<ref bean="someBean"/>
```
通过parent属性指定目标bean是创建对在当前容器的父容器中bean的引用。parent属性的值可能与目标bean的id属性或目标bean的name属性中的值之一相同，并且目标bean必须在当前容器的父容器中。你使用这种引用形式变体，主要在当你有层次结构的容器，并且你想要使用代理包装一个在父容器中已有的bean，这样会与父bean具有相同的名称。
```xml
<!-- 在父上下文中 -->
<bean id="accountService" class="com.foo.SimpleAccountService">
    <!-- 根据需要插入依赖 -->
</bean>
```
```xml
<!-- 在子（后裔）上下文中 -->
<bean id="accountService" <!-- bean名称与父bean相同 -->
    class="org.springframework.aop.framework.ProxyFactoryBean">
    <property name="target">
        <ref parent="accountService"/> <!-- 注意我们如何引用父bean -->
    </property>
    <!-- 在这里按需要插入其他配置和依赖 -->
</bean>
```
> 在4.0 beans xsd中，idref元素上的local属性不再受支持，因为它不再提供超过常规bean引用的值。在升级到4.0 schema时，只需将您现有的idref local引用更改为idref bean。

### 内部bean
一个在&lt;property/>或&lt;constructor-arg/>元素内部的&lt;bean/>元素定义了一个所谓的内部bean。
```xml
<bean id="outer" class="...">
    <!-- 相比使用一个目标bean的引用，可以只是内联定义这个目标bean -->
    <property name="target">
        <bean class="com.example.Person"> <!-- 这是一个内部bean -->
            <property name="name" value="Fiona Apple"/>
            <property name="age" value="25"/>
        </bean>
    </property>
</bean>
```
一个内部bean定义不需要定义的id或name;如果指定的话，容器也不使用这样的值作为标识符。容器也会忽略创建的scope标志：内部bean总是匿名的，并且它们总是外部bean而创建。不可能将注入内部bean到除闭包bean外的其他协作bean中，也不可能独立访问它们。

作为一个个别案例，有可能从一个自定义域中接收销毁回调，例如，对一个包含在单例bean中的request-scoped内部bean：内部Bean实例的创建将绑定到其包含的bean上但是销毁回调允许它参与到request scope的生命周期。这不是常见场景；内部bean通常只是分享在它们的包含bean的域内。
### 集合
在&lt;list/>，&lt;set/>，&lt;map/>和&lt;props/>元素中，您可以分别设置Java Collection类型List，Set,Map和Properties的属性和参数。
```xml
<bean id="moreComplexObject" class="example.ComplexObject">
    <!-- 导致setAdminEmails(java.util.Properties)调用 -->
    <property name="adminEmails">
        <props>
            <prop key="administrator">administrator@example.org</prop>
            <prop key="support">support@example.org</prop>
            <prop key="development">development@example.org</prop>
        </props>
    </property>
    <!--导致setSomeList(java.util.List)调用 -->
    <property name="someList">
        <list>
            <value>a list element followed by a reference</value>
            <ref bean="myDataSource" />
        </list>
    </property>
    <!-- 导致setSomeMap(java.util.Map)调用 -->
    <property name="someMap">
        <map>
            <entry key="an entry" value="just some string"/>
            <entry key ="a ref" value-ref="myDataSource"/>
        </map>
    </property>
    <!-- 导致setSomeSet(java.util.Set)调用 -->
    <property name="someSet">
        <set>
            <value>just some string</value>
            <ref bean="myDataSource" />
        </set>
    </property>
</bean>
```
*Map的key和value的值，或set的值，也可以是以下任意一元素*
```
bean | ref | idref | list | set | map | props | value | null
```
#### 集合合并
Spring容器还支持集合的合并。应用程序开发人员可以定义父样式的&lt;list/>，&lt;map/>，&lt;set/>或&lt;props/>元素，并且有子样式的&lt;list/>，&lt;map/>，&lt;set/>或&lt;props/>元素继承并重写父集合中的值。也就是说，子集合的值是合并父和子集合的元素的结果，子集合元素重写在父集合中指定的值。

*关于合并的这个部分讨论了父子bean的机制。读者不熟悉父和子bean定义的可能需要在继续前阅读 [相关章节](Bean定义继承)*

以下示例演示集合合并：
```xml
<beans>
    <bean id="parent" abstract="true" class="example.ComplexObject">
        <property name="adminEmails">
            <props>
                <prop key="administrator">administrator@example.com</prop>
                <prop key="support">support@example.com</prop>
            </props>
        </property>
    </bean>
    <bean id="child" parent="parent">
        <property name="adminEmails">
            <!-- 合并在子集合定义上指定 -->
            <props merge="true">
                <prop key="sales">sales@example.com</prop>
                <prop key="support">support@example.co.uk</prop>
            </props>
        </property>
    </bean>
<beans>
```
请注意，在子bean定义的adminEmails属性的&lt;props/>元素上使用merge=true属性。当子bean被容器解析并实例化的时候，生成的实例具有一个adminEmails Properties集合，该集合包含将子级的adminEmails集合与父级的adminEmails集合合并的结果。
```
administrator=administrator@example.com
sales=sales@example.com
support=support@example.co.uk
```
子Properties集合的值集继承父项&lt;props/>中的所有属性元素，并且子级support的值覆盖父集合中的值。

此合并行为与&lt;list/>，&lt;map/>和&lt;set/>集合类型类似。在具体&lt;list/>元素的情况下，与List集合类型相关联的语义，也就是说，有序集合概念的值会被维持;父项的值先于所有子列表的值。在Map，Set和Properties集合类型的情况下，不存在排序。因此，对于容器在内部使用的在相关的Map，Set和Properties实现类型的基础之上实现的集合类型，没有有效的有序语义。

**集合合并的限制**
您不能合并不同的集合类型（如一个Map和一个List），如果您尝试这样做，则会抛出合适的异常。merge 属性必须在较低的，继承的子定义上指定;在父集合定义上指定合并属性是多余的，不会导致期望的合并。
**强类型集合**
随着在Java 5中引入泛型类型，你可以使用强类型集合。也就是说，可以声明一个Collection类型，使它只能包含String元素（举例）。如果您使用Spring来依赖注入一个强类型的Collection到一个bean中，您可以利用Spring的类型转换支持，以便将强类型的Collection实例的元素转换为适当的类型，然后才能添加到Collection中。
```java
public class Foo {

    private Map<String, Float> accounts;

    public void setAccounts(Map<String, Float> accounts) {
        this.accounts = accounts;
    }
}
```
```xml
<beans>
    <bean id="foo" class="x.y.Foo">
        <property name="accounts">
            <map>
                <entry key="one" value="9.99"/>
                <entry key="two" value="2.75"/>
                <entry key="six" value="3.99"/>
            </map>
        </property>
    </bean>
</beans>
```
当foo bean的accounts属性准备注入时，有关强类型Map&lt;String，Float>的元素类型的泛型信息可通过反射获得。因此，Spring的类型转换基础框架将各种值元素识别为Float类型，并将字符串值9.99,2.75和3.99转换为实际的Float类型。

### null和空字符串值
Spring将属性的空参数作为空字符串处理。以下基于XML的配置元数据片段将电子邮件属性设置为空字符串值（“”）。
```xml
<bean class="ExampleBean">
    <property name="email" value=""/>
</bean>
```
上面实例等效与下面的Java代码
```java
exampleBean.setEmail("")
```
&lt;null/>元素处理null值。例如：
```xml
<bean class="ExampleBean">
    <property name="email">
        <null/>
    </property>
</bean>
```
上述配置相当于以下Java代码：
```java
exampleBean.setEmail(null)
```
### 使用p命名空间的XML快捷方式
p命名空间使您能够用bean元素的属性而不是嵌套的&lt;property/>元素来描述属性值和/或协作bean。

Spring使用命名空间支持可扩展的配置格式，这是基于XML Schema 定义。本章讨论的beans配置格式在XML Schema文档中定义。但是，p命名空间没有在XSD文件中定义，只存在于Spring的内核。

以下示例显示了解决相同结果的两个XML片段：第一个使用标准XML格式，第二个使用p命名空间。
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean name="classic" class="com.example.ExampleBean">
        <property name="email" value="foo@bar.com"/>
    </bean>

    <bean name="p-namespace" class="com.example.ExampleBean"
        p:email="foo@bar.com"/>
</beans>
```
该示例显示了在bean定义中称为email的p命名空间中属性。这告诉Spring包含一个属性声明。如前所述，p命名空间没有schema定义，因此您可以将属性（attribute）的名称设置为属性（property ）名称。

下一个示例包括两个对另一个bean的引用的bean定义：：
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean name="john-classic" class="com.example.Person">
        <property name="name" value="John Doe"/>
        <property name="spouse" ref="jane"/>
    </bean>

    <bean name="john-modern"
        class="com.example.Person"
        p:name="John Doe"
        p:spouse-ref="jane"/>

    <bean name="jane" class="com.example.Person">
        <property name="name" value="Jane Doe"/>
    </bean>
</beans>
```
您可以看到，此示例不仅包括使用p命名空间的属性值，还使用特殊格式来声明属性引用。第一个bean定义使用&lt;property name ="spouse" ref ="jane"/>来创建从bean john到bean jane的引用，第二个bean定义使用p:spouse-ref ="jane"作为属性来完成相同的事情。在这种情况下，spouse是属性名称，而-ref部分表示这不是一个直接的值，而是引用另一个bean。
> p命名空间不如标准XML格式那么灵活。例如，这种声明属性引用的格式与以Ref结尾的属性冲突，而标准XML格式则不会。我们建议您仔细选择您的方法，并将其传达给您的团队成员，以避免同时使用所有三种方法生成XML文档

### 使用c命名空间的XML快捷方式

类似于称为“使用p命名空间的XML快捷方式”，Spring 3.1中新引入的c命名空间允许使用内联属性来配置构造函数参数，而不是使用嵌套的constructor-arg元素。

我们来回顾一下名为[“基于构造函数的依赖注入”](#基于构造函数的依赖注入)一节中使用c:namespace的例子：
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:c="http://www.springframework.org/schema/c"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="bar" class="x.y.Bar"/>
    <bean id="baz" class="x.y.Baz"/>

    <!-- 传统的声明 -->
    <bean id="foo" class="x.y.Foo">
        <constructor-arg ref="bar"/>
        <constructor-arg ref="baz"/>
        <constructor-arg value="foo@bar.com"/>
    </bean>

    <!-- 使用c命名空间的声明 -->
    <bean id="foo" class="x.y.Foo" c:bar-ref="bar" c:baz-ref="baz" c:email="foo@bar.com"/>

</beans>
```
对于通过参数名来设置构造函数参数，c:命名空间与p:的使用相同的惯例（对bean引用的尾接-ref）。同样，它也需要声明，即使它没有在XSD schema 中定义（但它存在于Spring内核中）。

对于构造函数参数名称不可用的罕见情况（通常如果字节码在没有调试信息的情况下编译），可以对参数索引使用回退：
```xml
<!-- c命名空间索引声明 -->
<bean id="foo" class="x.y.Foo" c:_0-ref="bar" c:_1-ref="baz"/>
```
> 由于XML语法，索引符号需要前置\_的存在，因为XML属性名称不能以数字开头（即使某些IDE允许）。

在实践中，构造函数解析机制在匹配参数方面非常有效，除非真的需要，我们建议你在你的配置宗始终使用名称符号。

### 复合属性名称
当您设置bean属性时，可以使用复合或嵌套属性名称，只要除了最后属性名称之外的路径的所有组件都不为null。 考虑下面的bean定义。
```xml
<bean id="foo" class="foo.Bar">
    <property name="fred.bob.sammy" value="123" />
</bean>
```
foo bean有一个fred属性，fred属性又有一个bob属性，bob属性又有一个sammy属性，最后的sammy属性被设置值为123。为了这个工作，在bean被构建之后，foo的fred属性，fred的bob属性必须不是null，否则抛出一个NullPointerException 。
   
## 使用depends-on
***
如果一个bean是另一个的依赖，通常意味着一个bean被设置为另一个bean的属性。通常，您可以使用基于XML的配置元数据中的<ref />元素来完成此任务。但是，有时bean之间的依赖关系较不直接;比如，在一个类中的静态初始化器需要被触发，例如数据库驱动注册。depends-on属性可以明确强制一个或多个bean在使用此元素的bean被初始化前先初始化。下面示例使用depends-on熟悉来表示一个单例bean上的依赖：
```xml
<bean id="beanOne" class="ExampleBean" depends-on="manager"/>
<bean id="manager" class="ManagerBean" />
```
为了表达对多个bean的依赖，提供bean名称列表作为depends-on属性的值，使用逗号，空白或者分号作为有效分隔符。
```xml
<bean id="beanOne" class="ExampleBean" depends-on="manager,accountDao">
    <property name="manager" ref="manager" />
</bean>

<bean id="manager" class="ManagerBean" />
<bean id="accountDao" class="x.y.jdbc.JdbcAccountDao" />
```
> 在bean定义中的depends-on属性可以同时指定初始化时依赖，而在单例Bean的情况下，可以指定相应的销毁时依赖。在给定的bean本身被销毁之前，定义与给定bean的依赖关系的依赖bean首先被销毁。因此depends-on也可以控制关机顺序。

## 懒加载bean
***
默认情况下，ApplicationContext实现在初始化过程中急切创建和配置所有单例bean。一般来说，这种预实例化是可取的，因为配置或周围环境中的错误会被立即发现，而不是在几个小时甚至几天之后。当不希望这种行为的时候，你通过可以标记bean定义为懒加载来组织单例bean的预实例化。一个懒加载的bean告诉IoC容器当它被第一次请求的时候才创建bean实例，而不是在启动时就实例化。

在XML中，这种行为由在&lt;bean/>元素上的lazy-init属性控制；例如：
```xml
<bean id="lazy" class="com.foo.ExpensiveToCreateBean" lazy-init="true"/>
<bean name="not.lazy" class="com.foo.AnotherBean"/>
```
当上述配置被ApplicationContext使用时，ApplicationContext启动时，名为lazy的bean不会立即被预实例化，而not.lazy bean会被立即预实例化。

然而，当一个懒加载的bean是一个单例bean的依赖的时候，它不是懒加载的，ApplicationContext在启动时就创建该懒加载bean，因为它必须满足该单例的依赖。懒加载bean被注入到其他不是懒加载的单例bean中。

你也可以通过使用&lt;beans/>元素上的default-lazy-init属性在容器级别上控制懒加载；例如：
```xml
<beans default-lazy-init="true">
    <!-- 没有beans会被预实例化... -->
</beans>
```
## 自动装配协作者
***
Spring容器可以自动关联协作bean。你可以通过检查ApplicationContext的内容来允许Spring为你的bean自动解析协作者（其他bean）。自动装配有以下的优点：
- 自动装配可以显著减少指定属性或构造函数参数的需要。（其他机制，如本章其他地方讨论的bean模板在这方面也很有价值。）
- 自动装配可以随着对象的发展而更新配置。例如，如果你需要给一个类添加一个依赖，则可以无需修改配置，自动满足该依赖关系。因此，自动装配在开发过程中特别有用，在代码库变得更加稳定时，无需切换到显式装配。

当使用基于XML的配置元数据时，你使用&lt;bean/>元素上的autowire属性为bean定义指定自动装配模式。自动装配功能有四种模式。您可以指定自动装配每一个bean并且因此可以选择哪一个来自动装配。

|模式|解释|
|----|----|
|no|(默认)不自动装配。必须通过ref元素定义bean引用。对于大规模的部署不推荐改变此默认设置，因为明确指定协作者将给予更大的控制和清晰度。在某种程度上，它记录了系统的结构。|
|byName|通过属性名自动装配。Spring会找到一个与需要自动装配的属性名称相同的bean。例如，如果bean定义被设置为通过名称自动装配，并且它包含一个master属性（也就是，它有一个setMaster(..)方法），Spring会查找一个名称为master的bean定义，并且使用它来设置属性|
|byType|如果确实容器中存在该属性类型的一个bean，则允许该属性被自动装配。如果有超过一个的bean存在，会抛出一个致命异常，这表示您可能不会为该bean使用*byType*自动装配。如果这里没有匹配的bean存在，则什么也不会发生；属性没有被设置。|
|constructor|类似与*byType*，不但是只提供给构造函数参数。如果在容器中没有一个构造函数参数类型的bean，则会引起一个致命错误。|


在*byType* 或*constructor*模式下，你可以装配数组和类型集合。在这种情况下，容器中所有在匹配预期类型的自动装配候选者，都被提供来满足依赖。如果期望的key类型是String，你可以自动装配强类型的Map。一个自动装配Map值会由匹配预期类型的所有bean实例组成，并且Map的key会包含相应的bean名称。

您可以将autowire行为与依赖检查相结合，检查会在自动装配完成后执行。

### 自动装配的限制和缺点
在项目中贯穿始终的使用自动装配是效果最好的。如果自动装配没有被一贯使用，只是自动装配一两个bean定义可能会迷惑开发者。

考虑下面自动装配的限制和缺点：
- 在property和constructor-arg中显示设置的依赖总是覆盖自动装配。您不能自动装配所谓的简单属性，例如原始类型，String和Class（以及这些简单属性的数组）。这种限制是设计的。
- 自动装配不如显示写明精确。尽管，如上表所示，Spring小心避免在歧义的情况下猜测，这可能会有一个意想不到的结果，你的Spring管理对象之间的关系不再被明确记录。
- 对于可能从Spring容器生成文档的工具可能无法使用装配信息。
- 容器中的多个bean定义可能与由setter方法或构造函数参数指定的类型相匹配，从而进行自动装配。对于数组，集合或Map，这不一定是问题。然而，对于期望单个值的依赖，这种歧义不是任意解决的。如果没有唯一的bean定义可用，则抛出异常。

在后一种情况下，您有几个选项：
- 放弃自动装配，明确写明依赖。
- 通过设置一个bean的autowire-candidate属性为false（如下一节描述），避免该bean被自动装配。
- 通过将其&lt;bean/>元素的primary属性设置为true，将单个bean定义指定为主要候选。
- 使用基于注解的配置实现更细粒度的控制，如[“基于注解的容器配置”]()中所描述的。

### 从自动装配中排除一个bean
在每个bean的基础上，您可以将bean从自动装配中排除。在Spring的XML格式中，将&lt;bean/>元素上的autowire-candidate属性设置为false;容器使这个指定的bean定义不可用于自动装配基础架构（包括注解样式配置，例如@Autowired）。

> autowire-candidate属性被设计为仅影响基于类型的自动装配。它不会影响通过名称的明确引用，即使指定的bean没有被标记为一个自动装配候选，它也会被解析。因此，如果名称匹配的话，通过名称的自动装配依旧会注入这个bean。

你也可以基于模式匹配而不是bean名称来限制自动装配的候选者。顶级&lt;beans/>元素的default-autowire-candidates属性接收一个或多个模式。例如，要将自动装配候选者状态限制为任何名称以Repository结尾的bean，提供一个 \*Repository的值。要提供多个模式，在一个以逗号分割的列表中定义它们。对一个bean明确定义autowire-candidate属性的值为true或false的，这种设置始终优先，对于这样的bean，模式匹配将不再适用。

这些技术对于您不想通过自动装配注入其他bean的bean很有用。这并不意味着被排除的bean本身不能使用自动装配来配置。而是，这个bean它本身不是其他bean自动装配的候选者。

## 方法注入
***
在大多数应用程序场景中，在容器中大多数的bean都是 [单例]() 的。当一个单例bean需要和其他单例bean相协作时，或者一个非单例bean需要和其他非单例bean相协作时，你通常通过定义一个bean作为其他bean的属性来处理依赖关系。当bean的生命周期不同的时候，会发生一个问题。假设单例bean A需要使用非单例（原型）bean B，也许每个方法在A上调用。容器只会创建单例bean A一次，因此只有一次设置属性的机会。容器不能在每次需要时，向bean A提供一个新的bean B实例。

解决方案是放弃一些控制反转。你可以通过实现ApplicationContextAware接口使bean A对容器敏感，并且在每次bean A需要bean B的时候通过对容器调用getBean("B")来获取（通常是新的）bean B实例。以下是这种方法的一个例子：
```java
// 一个使用状态命令行样式类的类执行一些处理
package fiona.apple;

// Spring-API导入
import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;

public class CommandManager implements ApplicationContextAware {

    private ApplicationContext applicationContext;

    public Object process(Map commandState) {
        // 抓取相应Command的新实例
        Command command = createCommand();
        // 设置（希望全新）Command实例的状态
        command.setState(commandState);
        return command.execute();
    }

    protected Command createCommand() {
        // 注意Spring API依赖!
        return this.applicationContext.getBean("command", Command.class);
    }

    public void setApplicationContext(
            ApplicationContext applicationContext) throws BeansException {
        this.applicationContext = applicationContext;
    }
}
```
上述是不可取的，因为业务代码接触并耦合到Spring框架上。方法注入，一个Spring IoC容器的先进的功能，可以以干净的方式处理这种用例。

> 您可以在此 [博客条目](https://spring.io/blog/2004/08/06/method-injection/) 中阅读更多关于方法注入的动机。

### Lookup方法注入
Lookup方法注入是容器重写容器管理bean上方法的能力，用来返回容器中另一个命名bean的lookup结果。lookup通常涉及前一部分中描述的场景中的原型bean。Spring框架通过使用CGLIB库中的字节码生成来动态生成覆盖该方法的子类来实现此方法注入。
> - 为了使这个动态子类工作起来，Spring将子类化的类不能是final，并且被重写的方法也不能是final。
- 单元测试具有抽象方法的类需要您自己对类进行子类化，并提供抽象方法的存根实现。
- 组件扫描也需要具体的方法，这需要具体的类来进行。
- 另一个关键的限制是，lookup方法不能和工厂方法一起使用，并且特别是不能和配置类中的带有@Bean方法一起使用，因为在该情况下容器不负责创建实例，因此无法动态创建一个运行时生成的子类。

看前面的代码片段中的CommandManager类，你会看到Spring容器将动态地覆盖createCommand()方法的实现。您的CommandManager类将不会对Spring有任何依赖关系，在重做示例中可以看到：
```java
package fiona.apple;

// 不再有Spring导包!

public abstract class CommandManager {

    public Object process(Object commandState) {
        // 抓取相应Command接口的新实例
        Command command = createCommand();
        // 设置（希望全新）Command实例的状态
        command.setState(commandState);
        return command.execute();
    }

    // 好吧...但是这个方法的实现在哪里？
    protected abstract Command createCommand();
}
```
在客户端类包含要被注入的方法（在这个例子中是CommandManager），要注入的方法需要以下形式的签名：
```
<public|protected> [abstract] <return-type> theMethodName(no-arguments);
```
如果这个方法是抽象的，动态生成子类实现了这个方法。否则，动态生成子类覆盖这个定义在原始类中的具体方法。比如：
```xml
<!-- 被部署为一个原型（非单例）的有状态的类 -->
<bean id="myCommand" class="fiona.apple.AsyncCommand" scope="prototype">
    <!-- 按需在这里注入依赖 -->
</bean>

<!-- commandProcessor 使用statefulCommandHelper -->
<bean id="commandManager" class="fiona.apple.CommandManager">
    <lookup-method name="createCommand" bean="myCommand"/>
</bean>
```
标识为commandManager的bean无论什么时候需要myCommand bean的一个新实例，就调用自己的creatCommond()方法。您必须小心以原型部署myCommand bean，如果它实际需要。如果它是以单例部署，则每次myCommand bean返回的是同一个实例。

或者，在基于注解的组件模型中，您可以通过@Lookup注释声明一个lookup 方法：
```java
public abstract class CommandManager {

    public Object process(Object commandState) {
        Command command = createCommand();
        command.setState(commandState);
        return command.execute();
    }

    @Lookup("myCommand")
    protected abstract Command createCommand();
}
```
或者，更为惯用的是，你可能依赖针对lookup方法声明返回类型的解析来获取目标bean：
```java
public abstract class CommandManager {

    public Object process(Object commandState) {
        MyCommand command = createCommand();
        command.setState(commandState);
        return command.execute();
    }

    @Lookup
    protected abstract MyCommand createCommand();
}
```
请注意，您通常会使用具体的存根实现来声明这种带注解的lookup方法，为了使它们与Spring的组件扫描规则兼容，默认情况下抽象类被忽略。此限制不适用于明确注册或明确导入的bean类的情况。

> 另一种访问不同域的目标bean的方法是ObjectFactory/Provider注入点。查看[“作用域bean作为依赖”](#作用域bean作为依赖)一节。   
感兴趣的读者还可以找到ServiceLocatorFactoryBean（在org.springframework.beans.factory.config包中）来使用。

### 任意方法替换
比lookup方法注入更少用的一种方法注入形式是可以使用另一个方法实现来替换被管理bean中的任意方法。用户可以安全地跳过本节的剩余部分，直到实际需要该功能。

使用基于XML的配置元数据，对于已部署的bean你可以使用replacement-method元素将现有的方法实现替换为另一个。考虑下面的类，使用一个我们要重写的方法computeValue：
```java
public class MyValueCalculator {

    public String computeValue(String input) {
        // 一些真实代码...
    }

    // 一些其他方法...

}
```
实现org.springframework.beans.factory.support.MethodReplacer接口的类提供了新的方法定义。
```java
/**
 * 意在用于重写在MyValueCalculator
 * 中现有的computeValue(String)实现
 */
public class ReplacementComputeValue implements MethodReplacer {

    public Object reimplement(Object o, Method m, Object[] args) throws Throwable {
        // 获取输入值，使用它，并返回一个计算结果
        String input = (String) args[0];
        ...
        return ...;
    }
}
```
部署原始类并指定方法重写的bean定义如下所示：
```xml
<bean id="myValueCalculator" class="x.y.z.MyValueCalculator">
    <!-- 任意方法替换 -->
    <replaced-method name="computeValue" replacer="replacementComputeValue">
        <arg-type>String</arg-type>
    </replaced-method>
</bean>

<bean id="replacementComputeValue" class="a.b.c.ReplacementComputeValue"/>
```
您可以在&lt;replaced-method/>元素中使用一个或多个包含的&lt;arg-type/>元素来指示被重写的方法的方法签名。只有当方法重载并且类中存在多个变体时，参数的签名才是必需的。为方便起见，参数的类型字符串可能是完全限定类型名称的子字符串。例如，以下全部匹配java.lang.String：
```
java.lang.String
String
Str
```
由于参数的数量通常足以区分每个可能的选择，所以此快捷方式可以节省大量的打字，只需键入与参数类型匹配的最短字符串即可。

***
# Bean域
***
当你创建一个bean定义的时候，你创建了一个用于创建了一个由bean定义所定义的类的实际实例配方。bean定义是一个配方的概念很重要，因为这意味着，与类一样，您可以从单个配方创建许多对象实例。

你不仅可以控制要插入到由一个特殊bean定义创建的对象中的各种依赖和配置值，也可以控制这个对象的域。这种方法是强大和灵活的，您可以通过配置来选择您创建的对象的域，而不必在Java类级别上设置对象的域。bean可以被定义为部署在多个域之一中：Spring框架支持七个开箱即用的域，其中五个域只在你使用Web感知的ApplicationContext时可用。

以下域支持开箱即用。您还可以创建自定义域。

|域|描述|
|--|----|
|[singleton](#singleton域)|（默认）每个Spring IoC容器将单个bean定义作为单个对象实例。|
|[prototype](#prototype域)|将单个bean定义范围适用于任何数量的对象实例。|
|[request](#request域)|将单个bean定义范围适用于单个HTTP请求的生命周期;也就是，每个HTTP请求都有一个它自己的bean实例，该实例是由背后的单个bean定义创建的。只有在一个Web感知的Spring ApplicationContext的上下文中才有效。|
|[session](#session域)|将单个bean定义范围适用于一个HTTP Session生命周期中。只有在一个Web感知的Spring ApplicationContext的上下文中才有效。|
|[globalSession](#globalSession域)|将单个bean定义范围适用于一个HTTP Session生命周期中。通常仅在Portlet上下文中使用时才有效。只有在一个Web感知的Spring ApplicationContext的上下文中才有效。|
|[application](#application域)|将单个bean定义范围适用于一个ServletContext生命周期中。只有在一个Web感知的Spring ApplicationContext的上下文中才有效。|
|[websocket](#websocket域)|将单个bean定义范围适用于一个WebSocket生命周期中。只有在一个Web感知的Spring ApplicationContext的上下文中才有效。|

> 从Spring 3.0开始，一个thread域是可用的，但默认情况下未注册。有关更多信息，请参阅SimpleThreadScope的文档。有关如何注册此或任何其他自定义域的说明，请参阅[“使用自定义范围”](#使用自定义范围)一节。

## singleton域
***
只有一个单例bean的一个共享实例被管理，并且所有对具有该id的bean或匹配到id的bean的请求都会使Spring容器返回一个指定的bean实例。

换句话说，当你定义一个bean定义以及它的域为singleton时，Spring IoC容器只会创建一个由该bean定义定义的对象的实例。这个单例实例被储存在一个这样的单例bean的缓存中，并且所有对这个命名bean的后续请求和引用都会返回缓存对象。
![单例bean](/images/spring/singleton.png.pagespeed.ce.U0lSEQUK39.png)
Spring的单例bean概念不同于定义在Gang of Four (GoF)模式书中的单例模式（Singleton pattern）. GoF单例硬编码了对象的域范围，使得每个ClassLoader只会创建一个且只有一个特定类的实例。Spring单例域是描述为一个容器一个bean。这意味着如果在单个Spring容器为一个特殊类定义了一个bean，那么Spring容器只会创建一个且只有一个由该bean定义的类的实例。单例域是Spring默认的域。在XML中要定义一个bean为单例，你应该这么写，如下：
```xml
<bean id="accountService" class="com.foo.DefaultAccountService"/>

<!-- 下面是等效的，尽管有些多余（单例域是默认设置）) -->
<bean id="accountService" class="com.foo.DefaultAccountService" scope="singleton"/>
```
## prototype域
***
部署非单例，原型域的bean使每次对该特定bean进行请求时创建一个新的bean实例。也就是在容器中这个bean被注入到其他bean，或者你通过getBean()方法调用请求它。一般来说，为所有有状态的bean使用原型域，为无状态的bean使用单例域。

下面图表说明了Spring原型域。一个数据访问对象（DAO）通常不被配置为原型，因为一个典型DAO不保持任何会话状态；这只是作者简单的重用单例域的图。
![原型bean](/images/spring/prototype.png)
下面示例在XML中定义一个bean为原型：
```xml
<bean id="accountService" class="com.foo.DefaultAccountService" scope="prototype"/>
```
与其他域相反，Spring不管理原型bean的完整生命周期：容器实例化，配置或以其他方式组装原型对象，并将其交给客户端，并不再记录该原型实例。因此，尽管不管是什么域，初始化生命周期回调方法在所有的对象上被调用，但是在原型的情况下，配置的销毁生命周期不被调用。客户端代码必须清理原型域对象并且是释放这个原型bean持有的昂贵资源。要使Spring容器释放原型bean持有的资源，尝试使用一个自定义的bean [post-processor](), 它涉及需要清理的bean。

在某些方面，Spring容器对原型域bean的作用是Java new操作符的替代。所有这之后的生命周期管理都必须由客户端处理。（有关Spring容器中bean的生命周期的详细信息，请参见第6.1节[“生命周期回调”](#生命周期回调)。）

## 依赖原型bean的单例bean
***
当你使用依赖原型bean的单例bean的时候，要意识到依赖关系是在实例化时解析的。因此，如果你依赖注入一个原型域bean到一个单例域的bean，一个新的原型bean被实例化，然后被依赖注入到这个单例bean中。这个原型实例是曾经提供给单例域实例的唯一实例。

然而，假设你想要这个单例域bean在运行时期，重复去获得这个原型bean的新实例。你不能依赖注入一个原型域bean到你的单例域bean中，因为这个注入只发生一次，在Spring容器实例化这个单例bean并且解析和注入它的依赖时。如果在运行时多次需要一个原型bean的新实例，请参见第4.6节 [“方法注入”](#方法注入)

##  Request, session, global session, application和 WebSocket域
***
只有使用Web感知的Spring **ApplicationContext**实现（如XmlWebApplicationContext），**request**, **session**, **globalSession**, **application**和 **webSocket**域才可用。如果你在常规Spring Ioc容器，如**ClassPathXmlApplicationContext**中见到这些域，那么会抛出一个**IllegalStateException**异常来抱怨一个未知的bean范围。

### 初始web配置
要支持在**request**, **session**, **globalSession**, **application**和 **webSocket**级别（web域bean）bean的域，在你定义你的bean之前，需要一些次要的初始配置。（对于标准域，单例和原型不需要这些初始设置。）

如何完成此初始设置取决于您的特定Servlet环境。

如果在Spring Web MVC中访问作用域bean，实际上，其中请求由Spring的 DispatcherServlet或DispatcherPortlet来处理，这样不需要特殊的设置：DispatcherServlet和DispatcherPortlet已经显示所有相关状态。

如果你使用Servlet 2.5 web容器，请求在Spring的DispatcherServlet之外处理（例如，当使用JSF或Struts），你需要注册**org.springframework.web.context.request.RequestContextListener** **ServletRequestListener**。对于Servlet 3.0+,可以通过WebApplicationInitializer接口以编程方式完成。或者，对于较旧的容器，请将以下声明添加到Web应用程序的web.xml文件中：
```xml
<web-app>
    ...
    <listener>
        <listener-class>
            org.springframework.web.context.request.RequestContextListener
        </listener-class>
    </listener>
    ...
</web-app>
```
或者，如果您的监听器设置有问题，请考虑使用Spring的RequestContextFilter。过滤器映射取决于周围的Web应用程序配置，因此必须根据需要进行更改。
```xml
<web-app>
    ...
    <filter>
        <filter-name>requestContextFilter</filter-name>
        <filter-class>org.springframework.web.filter.RequestContextFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>requestContextFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    ...
</web-app>
```
**DispatcherServlet**，**RequestContextListener**和**RequestContextFilter**都完成相同的事情，即将HTTP请求对象绑定到为该请求提供服务的线程上。这使得request-和session域的bean在调用链上进一步可用。

### Request域
考虑以下用于bean定义的XML配置：
```xml
<bean id="loginAction" class="com.foo.LoginAction" scope="request"/>
```
Spring容器通过使用loginAction 的bean定义为每个HTTP请求创建一个新的LoginAction bean实例。也就是说，loginAction bean的作用域在HTTP request级别上。你可以根据需要更改创建的实例的内部状态，因为从同一个loginAction bean定义创建的其他实例将不会在状态中看到这些更改;它们是特定于单独请求的。当请求完成处理时，作用于该请求的bean将被丢弃。

使用注解驱动组件或Java Config时，可以使用@RequestScope注解指定组件为request域。
```java
@RequestScope
@Component
public class LoginAction {
    // ...
}
```
### Session域
考虑以下用于bean定义的XML配置：
```xml
<bean id="userPreferences" class="com.foo.UserPreferences" scope="session"/>
```
Spring容器通过使用UserPreferences的bean定义为每个HTTP会话的生命周期创建一个新的UserPreferences bean实例。换而言之，userPreferences bean的作用域在HTTP Session级别上有效。和request域的bean一样，你可以根据需要更改创建的实例的内部状态，其他HTTP Session实例使用从相同userPreferences bean定义创建的实例看不到这些状态修改，因为它们是特定于单个HTTP会话的。当HTTP Session最终被丢弃时，范围限定于该特定HTTP Session的bean也被丢弃。

使用注解驱动组件或Java Config时，可以使用@SessionScope注解指定组件为session域。
```java
@SessionScope
@Component
public class UserPreferences {
    // ...
}
```
### globalSession域
考虑以下bean定义：
```xml
<bean id="userPreferences" class="com.foo.UserPreferences" scope="globalSession"/>
```
globalSession域类似于标准的HTTP Session域（如上描述），并且仅适用于基于Portlet的Web应用程序的上下文。portlet规范定义了构成单个Portlet Web应用程序的所有portlet之间共享的全局会话的概念。定义在globalSession域上的Bean被限定（或绑定）到全局portlet Session的生命周期上。

如果你编写一个标准的基于Servlet的web应用程序，并且你定义一个或多个bean具有gloablSession域，那么使用的是标准HTTP Session域，并且不会发生错误。
### Application域
考虑以下用于bean定义的XML配置：
```xml
<bean id="appPreferences" class="com.foo.AppPreferences" scope="application"/>
```
Spring容器通过使用AppPreferences的bean定义为整个web程序创建一个新的AppPreferences bean实例。换而言之，appPreferences bean的作用域在HTTP ServletContext级别上有效，作为一个常规的ServletContext属性存储。这有些类似域Spring单例bean，但是在两个重要方式上不同：它是每一个ServletContext一个单例，而不是每一个Springd的'ApplicationContext'（在任何给定的Web应用程序中可能有几个），并且它实际上是暴露的，因此可视为ServletContext属性。
使用注解驱动组件或Java Config时，可以使用@ApplicationScope注解指定组件为application域。
```java
@ApplicationScope
@Component
public class AppPreferences {
    // ...
}
```
### 作用域bean作为依赖
Spring IoC容器不仅管理对象（bean）的实例化，还管理协作者（或依赖）的装配。如果要将HTTP request域的bean（比如）注入到另一个更长生命域的bean中，您可以选择注入AOP代理来代替作用域bean。也就是说，您需要注入一个代理对象，该对象暴露与作用域对象相同的公共接口，但也可以从相关域（例如HTTP请求）中检索真实目标对象，并将方法调用委托给真实对象。

> 您还可以在域为单例的bean之间使用&lt;aop:scoped-proxy/>，然后该引用经过可序列化的中间代理，因此能够在反序列化上重新获得目标单例bean。当针对原型域的bean声明&lt;aop:scoped-proxy/>，共享代理上的每个方法调用将导致创建一个新的目标实例，然后调用被转发到该目标实例。  
此外，域代理不是以生命周期安全方式从较较小域访问bean的唯一方法。你也可以简单只声明你的注入点（即构造器/setter参数或者自动装配字段）为ObjectFactory&lt;MyTargetBean>,允许getObject()调用在每次需要时根据需要检索当前实例，而不必保留实例或分别存储它。JSR-330变体称为Provider，与Provider&lt;MyTargetBean>声明一起使用，并为每次检索尝试使用相应的get()调用。有关JSR-330的更多详细信息，请参阅此处。

以下示例中的配置只有一行，但是了解“为什么”以及它背后的“如何”是很重要的。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 一个HTTP Session域bean以一个代理暴露出来 -->
    <bean id="userPreferences" class="com.foo.UserPreferences" scope="session">
        <!-- 指示容器代理包围的bean -->
        <aop:scoped-proxy/>
    </bean>

    <!-- 一个使用上述代理注入到的单例域bean -->
    <bean id="userService" class="com.foo.SimpleUserService">
        <!-- 对代理的userPreferences bean的引用 -->
        <property name="userPreferences" ref="userPreferences"/>
    </bean>
</beans>
```
要创建这样一个代理，您将一个子&lt;aop:scoped-proxy/>元素插入作用域bean定义中(请参阅 [“选择要创建的代理类型”]() 一节和第41章 [基于XML架构的配置]())。为什么作用域在request, session, globalSession和自定义域级别上bean定义需要&lt;aop：scoped-proxy/>元素？让我们来检查下下面的单例bean定义并且与上述域所需要的定义的进行对比（注意下面userPreferences bean定义是不完整的）
```xml
<bean id="userPreferences" class="com.foo.UserPreferences" scope="session"/>

<bean id="userManager" class="com.foo.UserManager">
    <property name="userPreferences" ref="userPreferences"/>
</bean>
```
在上面的示例中，单例beanuserManager被注入进这个HTTP-Session域bean userPreferences的引用。这里的重点是userManager bean是单例：它只会被每个容器实例化一次，并且它的依赖（这里只有一个，userPreferences bean）同样只会被注入进一次。这意味着userManager bean将只对完全相同的userPreferences对象进行操作，也就是最初注入的那个。

当将一个短生存域bean注入到一个长生存域bean的时候，这不是你想要的行为，比如将一个HTTP Session域的协作bean作为依赖注入到一个单例bean中。相反，你需要一个单例userManager对象，并且对于HTTP Session的整个生命周期，你需要一个特定于所述HTTP Session的userPreferences对象。因此，容器创建一个对象，该对象暴露与UserPreferences类（理想情况下是UserPreferences实例的对象）完全相同的公共接口，该对象可从域机制（HTTP request，Session等）中获取真实的UserPreferences对象。这个容器将这个代理对象注入到userManager bean中，该bean不会意识到这个UserPreferences引用是一个代理。在这个例子中，当UserManager实例调用一个依赖注入的UserPreferences对象的方法时，它实际是调用代理上的方法。然后这个代理从（这个例子中）HTTP Session中获取真实UserPreferences对象，并且将方法调用委托给这个获取的真实UserPreferences对象上。

因此当将request-，session-和globalSession-域的bean注入到协作bean上时，你需要下面正确并且完整的配置：
```xml
<bean id="userPreferences" class="com.foo.UserPreferences" scope="session">
    <aop:scoped-proxy/>
</bean>

<bean id="userManager" class="com.foo.UserManager">
    <property name="userPreferences" ref="userPreferences"/>
</bean>
```
#### 选择要创建的代理类型
默认情况下，当Spring容器为使用&lt;aop:scoped-proxy/>元素标记的bean创建代理时，将创建一个基于CGLIB的类代理。
> CGLIB代理只拦截公共方法调用！不要在这样的代理上调用非公开方法;它们不会被委派给实际的作用域目标对象。

或者，您可以通过为&lt;aop:scoped-proxy/>元素的proxy-target-class属性的值指定为false，来配置Spring容器为这些作用域bean创建基于标准的JDK基于接口的代理。使用JDK基于接口的代理意味着你的应用程序类路径不需要额外的库来实现这种代理。然而，这也意味着作用域bean的类必须实现至少一个接口，并且所有被注入协作者的作用域bean必须通过它的一个接口引用该bean。
```xml
<!-- DefaultUserPreferences实现了 the UserPreferences接口 -->
<bean id="userPreferences" class="com.foo.DefaultUserPreferences" scope="session">
    <aop:scoped-proxy proxy-target-class="false"/>
</bean>

<bean id="userManager" class="com.foo.UserManager">
    <property name="userPreferences" ref="userPreferences"/>
</bean>
```
有关选择基于类或基于接口的代理的更多详细信息，请参见第11.6节[“代理机制”]()。

## 自定义域
***
bean域机制是可以拓展的；你可以定义你自己的域，或者甚至可以重定义现有的bean，虽然后者被认为是不好的做法，你不能覆盖内置的单例和原型域。
### 创建自定义域
要将自定义域集成到Spring容器中，需要实现org.springframework.beans.factory.config.Scope接口，它将在本节讲述。有关如何实现自己的范围的想法，请参阅随Spring框架本身提供的Scope实现和Scope javadocs，它更详细地解释了需要实现的方法。

Scope接口有四个从域中获取，移除，并且销毁对象的方法。

以下方法从底层域返回对象。例如，session域实现返回会话域中的bean（如果不存在，则该方法返回一个新bean实例，并将其绑定到会话以供将来引用）。
```java
Object get(String name, ObjectFactory objectFactory)
```
以下方法从底层域中删除该对象。例如，session域实现从底层会话中移除该会话域bean。应该返回对象，但如果没有找到指定名称的对象，则可以返回null。
```java
Object remove(String name)
```
以下方法注册域应当执行的回调，当它被销毁或者当这个域中指定的对象被销毁的时候。关销毁回调的更多信息，请参阅javadocs或Spring scope实现。
```java
void registerDestructionCallback(String name, Runnable destructionCallback)
```
以下方法获取底层域的对话标识符。该标识符对于每个域是不同的。对于会话域的实现，该标识符可以是session标识符。
```java
String getConversationId()
```
### 使用自定义域

在编写和测试一个或多个自定义Scope实现之后，您需要使Spring容器了解您的新作用域。以下方法是使用Spring容器注册新的Scope的中心方法：
```java
void registerScope(String scopeName, Scope scope);
```
此方法在ConfigurableBeanFactory接口上声明，它通过BeanFactory属性与Spring配合在大多数的ApplicationContext具体实现可用。

registerScope(..)方法的第一个参数是与域关联的唯一名称;Spring容器中这样的名字的例子就是singleton和prototype。registerScope(..)方法的第二个参数是你希望注册使用的自定义Scope实现的实际实例。

假设您编写了自定义的Scope实现，然后像下面一样将它注册。
> 下面的例子使用Spring中包含的SimpleThreadScope，但它默认情况下未注册。对于您自己的自定义Scope实现，示例将是一样的。

```java
Scope threadScope = new SimpleThreadScope();
beanFactory.registerScope("thread", threadScope);
```
然后，您创建遵循自定义域域规则的bean定义：
```xml
<bean id="..." class="..." scope="thread">
```
通过自定义域实现，你不限于只使用域的编程式注册。您还可以声明地使用CustomScopeConfigurer类来执行Scope注册：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">

    <bean class="org.springframework.beans.factory.config.CustomScopeConfigurer">
        <property name="scopes">
            <map>
                <entry key="thread">
                    <bean class="org.springframework.context.support.SimpleThreadScope"/>
                </entry>
            </map>
        </property>
    </bean>

    <bean id="bar" class="x.y.Bar" scope="thread">
        <property name="name" value="Rick"/>
        <aop:scoped-proxy/>
    </bean>

    <bean id="foo" class="x.y.Foo">
        <property name="bar" ref="bar"/>
    </bean>

</beans>
```
> 在FactoryBean实现中放置&lt;aop:scoped-proxy/>时，工厂bean本身是范围限定的，而不是从getObject()返回的对象。

***
# 定制bean性质
***
## 生命周期回调
***
要与容器的bean生命周期管理进行交互，可以实现Spring InitializingBean和DisposableBean接口。容器为前者调用afterPropertiesSet()，并为后者调用destroy()，以允许bean在初始化和销毁bean时执行某些操作。

> JSR-250 @PostConstruct和@PreDestroy注解通常被认为是在现代Spring应用程序中接收生命周期回调的最佳做法。使用这些注解意味着你的bean没有和Spring特定接口耦合。有关详细信息，请参见第7.9.8节[“@PostConstruct和@PreDestroy”](#@PostConstruct和@PreDestroy)。如果您不想使用JSR-250注解，但您仍然希望移除耦合，请考虑使用init-method和destroy-method对象定义元数据。

在内部，Spring 框架使用BeanPostProcessor实现来处理它可以找到的任何回调接口，并调用适当的方法。如果你需要自定义功能或者其他生命周期行为这些Spring没有提供开箱即用的，你可以实现一个自己的BeanPostProcessor。更多信息，请参阅[容器扩展点](# 容器扩展点)。

除了初始化和销毁回调之外，Spring管理的对象可能还实现Lifecycle接口，这样这些对象可以参与由容器自己的生命周期驱动的启动和关闭过程。

在本节描述生命周期回调接口。

### 初始化回调
**org.springframework.beans.factory.InitializingBean**接口允许在bean上所有必须属性已经被容器设置好后，执行初始化工作。**InitializingBean**接口指定了一个单一方法：
```java
void afterPropertiesSet() throws Exception;
```
建议你不要使用**InitializingBean**接口，因为它不必要地将代码耦合到Spring上。作为选择，使用**@PostConstruct**注解或指定一个POJO初始化方法。在基于XML配置元数据中，你使用**init-method**属性来指定具有void no-argument签名的方法的名称。在Java Config中，你可以使用**@Bean**的**initMethod**属性，请参阅[“接收生命周期回调”]()一节。例如，以下内容：
```xml
<bean id="exampleInitBean" class="examples.ExampleBean" init-method="init"/>
```
```java
public class ExampleBean {

    public void init() {
        // 做一些初始化工作
    }

}
```
这与下面完全一样
```xml
<bean id="exampleInitBean" class="examples.AnotherExampleBean"/>
```
```java
public class AnotherExampleBean implements InitializingBean {

    public void afterPropertiesSet() {
        // 做一些初始化工作
    }

}
```
但是第一种没有将代码与Spring耦合在一起。

### 销毁回调
实现**org.springframework.beans.factory.DisposableBean**接口允许bean在包含它的容器销毁时获取一个回调。**DisposableBean**接口指定单个方法：
```java
void destroy() throws Exception;
```
建议你不要使用**DisposableBean**接口，因为它不必要地将代码耦合到Spring上。作为选择，使用**@PreDestroy**注解或指定一个由bean定义支持的通用方法。在基于XML配置元数据中，你使用&lt;bean/>上的**destroy-method**属性。在Java Config中，你可以使用**@Bean**的**destroyMethod**属性，请参阅[“接收生命周期回调”]()一节。例如，以下定义：
```xml
<bean id="exampleInitBean" class="examples.ExampleBean" destroy-method="cleanup"/>
```
```java
public class ExampleBean {

    public void cleanup() {
        // 做一些销毁工作(比如释放池连接)
    }

}
```
它与下面完成一样
```xml
<bean id="exampleInitBean" class="examples.AnotherExampleBean"/>
```
```java
public class AnotherExampleBean implements DisposableBean {

    public void destroy() {
        // 做一些销毁工作(比如释放池连接)
    }

}
```
但是第一种没有将代码与Spring耦合在一起。

> 一个&lt;bean>元素的destroy-method属性可以被赋予一个特殊的（推断的）值，该值指示Spring在特定的bean类上自动检测公共的close或shutdown方法。（任何实现java.lang.AutoCloseable或java.io.Closeable的类会因此匹配）。这个特殊的（推测的）值也可以在&lt;beans>元素的default-destroy-method属性上设置，以将此行为应用于一整套bean（请参阅[“默认初始化和销毁方法”](#默认初始化和销毁方法)一节）。注意这是Java cofig的默认行为。

### 默认初始化和销毁方法
当您编写不使用Spring指定的**InitializingBean**和**DisposableBean**回调接口的初始化和销毁​​方法回调时，你通常用init(), initialize(), dispose()等这些名称。理想情况下，这些生命周期回调方法的名称贯穿整个项目，以便所有的开发者使用相同的方法名称和确保一致性。

你可以配置Spring容器去查找在每个bean上的命名初始化和销毁回调方法名称。这意味着你作为一个程序开发者，可以编写你的应用程序类，并且使用一个名为init()的初始化回调，而不用必在每个bean定义上配置init-method=“init”属性。Spring IoC容器在bean被创建的时候调用这个方法（并按照前面描述的标准生命周期回调契约）。这个功能同样为初始化和销毁方法回调执行一致的命名约定。

假设你的初始化回调方法名为init()并且销毁回调方法名为destory()。你的类将类似于下面例子中的类。
```java
public class DefaultBlogService implements BlogService {

    private BlogDao blogDao;

    public void setBlogDao(BlogDao blogDao) {
        this.blogDao = blogDao;
    }

    // 这是（不出意料的）初始化回调方法
    public void init() {
        if (this.blogDao == null) {
            throw new IllegalStateException("The [blogDao] property must be set.");
        }
    }

}
```
```xml
<beans default-init-method="init">

    <bean id="blogService" class="com.foo.DefaultBlogService">
        <property name="blogDao" ref="blogDao" />
    </bean>

</beans>
```
由于顶级&lt;beans />元素属性中default-init-method属性的存在，导致Spring IoC容器将bean上名为init的方法识别为初始化方法回调。当bean被创建和组装，如果这个bean类上有这样一个方法，它会在合适的时间被调用。

你通过使用顶级&lt;beans />元素上default-init-method属性类似的配置销毁方法回调（在XML上是这样）。

现有的bean类已经具有与约定不符的回调方法，您可以通过使用&lt;bean/>的init-method和destroy-method属性来指定（在XML中，是这样）方法名称来覆盖默认值。

Spring容器保证在bean所有的依赖被提供后，立即调用配置的初始化回调。因此这个初始化回调是在原始bean上被调用，这意味着AOP拦截器等尚未应用于该bean。目标bean首先被完全创建，然后才应用AOP代理（例如）和它的拦截器链。如果目标bean和代理被分开定义，您的代码甚至可以绕过代理，与原始目标bean进行交互。因此，将拦截器应用到init方法将会变得不一致，因为这样做会将目标bean的生命周期和它的代理/拦截器耦合在一起，并在代码直接与原始目标bean进行交互时留下奇怪的语义。


### 组合生命周期机制
从Spring 2.5开始，您有三种控制bean生命周期行为的选项：InitializingBean和DisposableBean回调接口;自定义init()和destroy()方法;和@PostConstruct和@PreDestroy注解。您可以组合这些机制来控制给定的bean。

> 如果为一个bean配置了多个生命周期机制，并且每个机制都配置了不同的方法名称，那么每个配置的方法都按照下面列出的顺序执行。但是，如果为超过一个这些生命周期机制配置了相同的方法名称，例如初始化方法init() ，该方法将如上一节所述的只执行一次。

使用了不同的初始化方法，为同一个bean配置的多个生命周期机制，如下被调用：
- 被@PostConstruct注解的方法
- 由InitializingBean回调接口定义的afterPropertiesSet()
- 自定义配置的init()方法

销毁方法以相同的顺序被调用

- 被@PreDestroy注解的方法
- 由DisposableBean 回调接口定义的destroy()
- 自定义配置的destroy()方法

### 启动和关闭回调
Lifecycle接口为任何具有自身生命周期需求的对象定义了必需方法（例如启动和停止一些后台进程）：
```java
public interface Lifecycle {

    void start();

    void stop();

    boolean isRunning();

}
```
任何Spring管理的对象都可以实现这个接口。然后，当ApplicationContext本身接收到启动和停止信号时，例如，对于运行时的停止/重新启动情况，它会将这些调用级联到在该上下文中定义的所有Lifecycle实现。它通过委托给LifecycleProcessor来执行此操作
```java
public interface LifecycleProcessor extends Lifecycle {

    void onRefresh();

    void onClose();

}
```
请注意，**LifecycleProcessor**本身是Lifecycle接口的扩展。它还增加了另外两种方法来对正在刷新和关闭的上下文进行反应。

> 请注意，常规的**org.springframework.context.Lifecycle**接口只是对明确的启动/停止通知的简单合同，并不意味着在上下文刷新时自动启动。考虑实现**org.springframework.context.SmartLifecycle**来代替，以便对特定bean的自动启动（包括启动阶段）进行细粒度的控制。此外，请注意，停止通知不能保证在销毁之前发生：在常规关机时，所有Lifecycle bean将在广泛的销毁回调传播之前首先收到停止通知;然而，在上下文生命周期的热刷新或中止刷新尝试时，只会调用destroy方法。

启动和关闭调用的顺序可能很重要。如果任何两个对象之间存在“depends-on”关系，则依赖方将在其依赖之后启动，并在其依赖之前停止。然而，有时直接依赖是未知的。您可能只知道某种类型的对象应该在另一种类型的对象之前开始。在这些情况下，**SmartLifecycle**接口定义了另一个选项，即在其父接口**Phased**上定义的getPhase()方法。
```java
public interface Phased {

    int getPhase();

}
```
```java
public interface SmartLifecycle extends Lifecycle, Phased {

    boolean isAutoStartup();

    void stop(Runnable callback);

}
```
当启动时，相位最低的对象首先启动，停止时则按照相反的顺序进行。因此，实现SmartLifecycle的对象，并且它的getPhase()方法返回值为Integer.MIN\_VALUE的，将是第一个启动和最后停止的对象（可能是因为它依赖于其他进程运行）。当考虑相位值时，知道任何没有实现**SmartLifecycle **的“正常”**Lifecycle**对象的相位值为0是很重要的。因此，任何负相位值都表示一个对象应该在这些标准组件之前开始（并在它们之后停止），反之亦然。

您可以看到**SmartLifecycle**定义的stop方法接受回调。任何实现必须在该实现的关闭过程完成后调用该回调的run（）方法。这可以在必要时进行异步关闭，因为**LifecycleProcessor**接口的默认**DefaultLifecycleProcessor**，会对每个阶段中的对象组等待它们的超时值，以此来调用这个回调。每个相位默认超时值是30秒。您可以通过在上下文中定义名为“lifecycleProcessor”的bean来覆盖默认的生命周期处理器实例。如果您只想修改超时值，则定义以下内容就足够了：
```xml
<bean id="lifecycleProcessor" class="org.springframework.context.support.DefaultLifecycleProcessor">
    <!-- 以毫秒为单位的超时值 -->
    <property name="timeoutPerShutdownPhase" value="10000"/>
</bean>
```
如前所述，LifecycleProcessor接口定义了用于刷新和关闭上下文的回调方法。后者将简单地驱动关闭过程，就像已经明确调用了stop()一样，但是它是在上下文关闭时发生。另一方面，“refresh”回调启用了SmartLifecycle bean的另一个功能。当上下文被刷新（在所有对象被实例化和初始化之后）时，该回调将被调用，并且在这个点上，默认生命周期处理器将检查每个SmartLifecycle对象的isAutoStartup()方法返回的布尔值。如果为“true”，则该对象将在该点启动，而不是等待上下文或它自己的start()方法的显式调用（与上下文刷新不同，对于标准的上下文实现上下文启动不会自动发生）。“phase”值以及任何“depends-on”关系将以与上述相同的方式决定来启动顺序。


### 在非Web应用程序中正常关闭Spring IoC容器
> 本节仅适用于非Web应用程序。 Spring的基于Web的ApplicationContext实现已经有代码在关闭相关Web应用程序时，正常关闭Spring IoC容器。

如果在非Web应用程序环境中使用Spring的IoC容器;例如，在富客户端桌面环境中;你为JVM注册一个关闭钩子。这样做可以确保正常关机，并在单例Bean上调用相关的destroy方法，以便释放所有资源。当然，您仍然必须正确配置和实现这些销毁回调。

要注册一个关闭钩子，你调用ConfigurableApplicationContext接口上声明的registerShutdownHook()方法：
```java
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public final class Boot {

    public static void main(final String[] args) throws Exception {

        ConfigurableApplicationContext ctx = new ClassPathXmlApplicationContext(
                new String []{"beans.xml"});

        // 为上述上下文添加一个关闭钩子...
        ctx.registerShutdownHook();

        // app在这里运行...

        // 主要方法退出，钩子在应用程序关闭之前被调用......

    }
}
```

## ApplicationContextAware和BeanNameAware
***
当**ApplicationContex**t创建一个实现**org.springframework.context.ApplicationContextAware**接口的对象实例时，会提供给实例该**ApplicationContext**的引用。
```java
public interface ApplicationContextAware {

    void setApplicationContext(ApplicationContext applicationContext) throws BeansException;

}
```
因此bean可以编程式操作创建它们的ApplicationContext ，通过**ApplicationContext**接口或者将个引用转换为这个接口的一个已知子类，例如**ConfigurableApplicationContext**，它暴露了一些额外功能。这样做的一个用途是可以编程式检索其他的bean。有时这种能力是有用的;然而，一般你应该避免这样做，因为它将代码和Spring耦合在一起，并且没有遵循将协作者作为属性提供给bean的控制反转风格。**ApplicationContext**的其他方法提供对文件资源的访问，发布应用程序事件和访问**MessageSource**。这些附加功能在第7.15节[“ApplicationContext的附加功能”]()中描述。

从Spring2.5开始，自动装配是另一种获取**ApplicationContext**引用的可选方法。“传统”的**constructor**和**byType**自动装配模式（如第4.5节[“自动装配协作者”](#自动装配协作者)中所述）可以分别为构造函数参数或setter方法参数提供**ApplicationContext**类型的依赖。为了更多的灵活性，包括自动装配字段和多参数方法的能力，请使用新的基于注解的自动装配功能。如果你这样做，**ApplicationContext**被自动装配到一个字段，构造函数参数或者方法参数上，如果相关期望**ApplicationContext**类型的字段，构造方法或方法带有@Autowired注解的话。更多信息，请参阅第9.2节[“@Autowired”](#@Autowired)。

当**ApplicationContext**创建一个实现**org.springframework.beans.factory.BeanNameAware**接口的类时，该类将被提供一个对其关联对象定义中定义名称的引用。
```java
public interface BeanNameAware {

    void setBeanName(String name) throws BeansException;

}
```
这个回调在普通bean属性被填充后，但是在一个初始化回调（如InitializingBean的afterPropertiesSet 或一个自定义init方法）前调用。

## 其他Aware接口
除了上面讨论的**ApplicationContextAware**和**BeanNameAware**之外，Spring还提供了一系列Aware接口，允许bean指示容器他们需要某个基础框架依赖。最重要的Aware接口总结如下-作为通用规则，它们的名称很好的指示了依赖类型：

|名称|注入的依赖|在...解释|
|----|-----------|---------|
|**ApplicationContextAware**|声明ApplicationContext|[第6.2节“ApplicationContextAware和BeanNameAware”](#ApplicationContextAware和BeanNameAware)|
|**ApplicationEventPublisherAware**|封闭的ApplicationContext事件发布`器|[7.15节“ApplicationContext的附加功能”](#ApplicationContext的附加功能)|
|**BeanClassLoaderAware**|用于加载bean类的类加载器。|[第3.2节“实例化bean”](#)|
|**BeanFactoryAware**|声明的BeanFactory|[第6.2节“ApplicationContextAware和BeanNameAware”](#ApplicationContextAware和BeanNameAware)|
|**BeanNameAware**|声明bean的名称|[第6.2节“ApplicationContextAware和BeanNameAware”](#ApplicationContextAware和BeanNameAware)|
|**BootstrapContextAware**|容器运行的资源适配器BootstrapContext。通常仅在JCA感知的ApplicationContexts中可用|[第32章，JCA CCI](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#cci)|
|**LoadTimeWeaverAware**|定义了加载时处理类定义的weaver|[](#)|
|**MessageSourceAware**|解析消息的配置策略（支持参数化和国际化）|[](#)|
|**NotificationPublisherAware**|Spring JMX通知发布器|[第31.7节“通知”](#)|
|**PortletConfigAware**|当前容器运行的PortletConfig。只有在Web感知的Spring ApplicationContext中才有效|[第25章Portlet MVC框架](#)|
|**PortletContextAware**|当前容器运行的PortletContext。只有在Web感知的Spring ApplicationContext中才有效|[第25章Portlet MVC框架](#)|
|**ResourceLoaderAware**|用于低级别访问资源配置的loader|[第8章 资源](#)|
|**ServletConfigAware**|当前容器运行的ServletConfig。只有在Web感知的Spring ApplicationContext中才有效|[第22章，Web MVC框架](#)|
|**ServletContextAware**|当前容器运行的ServletContext。只有在Web感知的Spring ApplicationContext中才有效|[第22章，Web MVC框架](#)|

***
# Bean定义继承
***
一个bean定义可以包含很多配置信息，包括构造函数参数，属性值和容器特定信息，如初始化方法，静态工厂方法名称等。子bean定义从父定义继承配置数据。子定义可以根据需要覆盖某些值，或者添加其他值。使用父和子bean定义可以节省大量的输入。实际上，这是模板的一种形式。

如果以编程方式使用ApplicationContext接口，子Bean定义由ChildBeanDefinition类表示。大多数用户不能在这个级别上使用它们，而是以类似于ClassPathXmlApplicationContext的方式声明地配置bean定义。当您使用基于XML的配置元数据时，你通过使用parent属性指定一个子bean的定义，指定父bean作为此属性的值。
```xml
<bean id="inheritedTestBean" abstract="true"
        class="org.springframework.beans.TestBean">
    <property name="name" value="parent"/>
    <property name="age" value="1"/>
</bean>

<bean id="inheritsWithDifferentClass"
        class="org.springframework.beans.DerivedTestBean"
        parent="inheritedTestBean" init-method="initialize">
    <property name="name" value="override"/>
    <!-- 值为1的age属性会从父级继承 -->
</bean>
```
一个自bean定义如果没有指定bean类的话则使用父级的定义，但也可以覆盖父级定义。在后一种情况，这个子bean必须兼容父级，也就是，它必须接受父级的属性值。

子bean继承父级的域，构造函数参数值，属性值，和方法重写，并可以添加新值。你指定的任何域，初始化方法，销毁方法，和/或静态工厂方法会覆盖父级中相应的设置。

其余设置始终采用自子定义：depends on，自动装配模式，依赖关系检查，单例，懒加载。

上面的例子使用**abstract**属性明确标示父bean定义是抽象的。如果父定义没有指定一个类，则需要将父bean定义明确标识为抽象，如下所示：
```xml
<bean id="inheritedTestBeanWithoutClass" abstract="true">
    <property name="name" value="parent"/>
    <property name="age" value="1"/>
</bean>

<bean id="inheritsWithClass" class="org.springframework.beans.DerivedTestBean"
        parent="inheritedTestBeanWithoutClass" init-method="initialize">
    <property name="name" value="override"/>
    <!-- age会从父bean定义中继承值为1-->
</bean>
```
父bean不能自己实例化，因为它是不完整的，它也被明确标记为**abstract**。当一个定义像这样是“abstract”的，它只能用作纯模板bean定义，作为子定义的父定义。尝试使用这样一个抽象的父bean，当通过引用它作为另一个bean的ref属性或使用父bean id进行显式的getBean()调用，会返回一个错误。类似地，容器的内部preInstantiateSingletons()方法忽略被定义为**abstract**的bean定义。

> **ApplicationContext**默认预实例化所有的单例。因此，重要的是（至少对于单例bean），如果您有一个（父）bean定义，您打算仅作为模板使用，并且这个定义指定了一个类，则必须确保将abstract属性设置为true，否则应用程序上下文将实际（尝试）预实例化这个抽象bean。



***
# 容器扩展点
***
通常，应用程序开发人员不需要对ApplicationContext实现类进行子类化。相反，Spring IoC容器可以通过插入特殊集成接口的实现来扩展。接下来的几节将介绍这些集成接口。
## 使用BeanPostProcessor自定义bean
***
**BeanPostProcessor**接口定义了你可以实现的回调方法，以提供自己的（或覆盖容器的默认）实例化逻辑，依赖解析逻辑等。如果要在Spring容器完成实例化，配置和初始化bean之后实现一些自定义逻辑，则可以插入一个或多个BeanPostProcessor实现。

你可以配置多个**BeanPostProcessor**实例，并且可以通过设置order属性来控制这些BeanPostProcessors执行的顺序。只有当**BeanPostProcessor**实现了**Ordered**接口时，才能设置此属性;如果你编写自己的**BeanPostProcessor**，你也应该考虑实现**Ordered**接口。有关更多详细信息，请参阅**BeanPostProcessor**和**Ordered**接口的javadocs。另请参阅下面的关于编程式注册**BeanPostProcessors**的注释。

> BeanPostProcessor操作bean（或对象）实例；也就是说，Spring Ioc容器实例化一个bean示例，然后BeanPostProcessor执行它的工作。
BeanPostProcessor作用域在每个容器范围。这只有在使用容器层次结构时才有用。如果您在一个容器中定义了一个BeanPostProcessor，那么它只会对该容器中的bean进行post-process。换句话说，在一个容器中定义的bean不会被另一个容器中定义的BeanPostProcessor进行post-process，即使这两个容器都是同一层次结构的一部分。    
要更改实际的bean定义（即定义bean的蓝图），您需要使用BeanFactoryPostProcessor，如第7.8.2节[“使用BeanFactoryPostProcessor自定义配置元数据”](#使用BeanFactoryPostProcessor自定义配置元数据)所述。

**org.springframework.beans.factory.config.BeanPostProcessor**接口只由两个回调方法组成。当这样的类在容器中被注册为post-processor时，对于由容器创建的每个bean实例，post-processor在容器初始化方法（诸如InitializingBean的afterPropertiesSet()和任何声明的init方法）被调用之前以及任何bean初始化回调之后，从容器中获取回调。post-processor可以对bean实例执行任何操作，包括完全忽略回调。bean post-processor通常检查回调接口，或者可能使用一个代理来包装bean。一些Spring AOP基础框架类被实现为bean post-processor，以提供代理包装逻辑。

ApplicationContext会自动探测任何定义在配置元数据中实现了BeanPostProcessor接口的bean。ApplicationContext注册这些bean作为post-processor，以便稍后在创建bean时可以调用它们。Bean post-processor可以像任何其他bean一样部署在容器中。

注意，当在配置类上使用@Bean工厂方法声明一个BeanPostProcessor时，工厂方法的返回类型应该是实现类本身或至少是org.springframework.beans.factory.config.BeanPostProcessor接口，清楚地表明该bean的post-processor性质。否则，ApplicationContext将无法在完全创建它之前按类型自动检测到它。由于为了在上下文中应用于其他bean的初始化，所以BeanPostProcessor需要更早的实例化，这种早期类型检测至关重要。

> 虽然BeanPostProcessor注册的推荐方式是通过ApplicationContext自动检测（如上所述），但也可以使用addBeanPostProcessor方法对ConfigurableBeanFactory进行编程式注册。当需要在注册之前评估条件逻辑，或者甚至在层次结构中的背景上复制bean post-processor时，这可能是有用的。但是请注意，以编程方式添加的BeanPostProcessors不遵循Ordered接口。这里注册的顺序决定执行的顺序。还要注意，以编程方式注册的BeanPostProcessors始终在通过自动检测注册的那些之前进行处理，而不管是否有明确的排序。

> 实现BeanPostProcessor接口的类是特殊的，并且被容器不同对待。作为ApplicationContext的特殊启动阶段的一部分，它们直接引用的所有BeanPostProcessors和Bean都将在启动时实例化。接下来，所有BeanPostProcessors都以排序的方式进行注册，并应用于容器中的所有其他bean。因为AOP自动代理以BeanPostProcessor本身实现，所以BeanPostProcessors和它们直接引用的bean都不具有自动代理的资格，因此没有组织它们的方面。    
对于任何这样的bean，您应该看到一个信息日志消息：“Bean foo不符合所有BeanPostProcessor接口处理（例如：不符合自动代理资格）”的资格。
注意，如果你让bean使用自动装配或者@Resource（它可能回退进行自动装配）装配到你的BeanPostProcessor，则Spring可能会在搜索类型匹配的候选对象时访问非预期的bean，从而使该BeanPostProcessor不符合自动代理或其他类型的bean  post-processing。例如，如果您使用@Resource注解的的依赖，其中字段/ setter名称与bean的已声明名称不直接对应，并且注解没有使用name属性，那么Spring将访问其他bean以按类型进行匹配。

以下示例说明如何在ApplicationContext中编写，注册和使用BeanPostProcessors。
### 示例：Hello World，BeanPostProcessor-style
***
第一个例子说明了基本用法。该示例展示了一个自定义BeanPostProcessor实现，它调用每个由容器创建的bean的toString()方法，，并将生成的字符串打印到系统控制台。

查找下面的自定义BeanPostProcessor实现类定义：
```java
package scripting;

import org.springframework.beans.factory.config.BeanPostProcessor;
import org.springframework.beans.BeansException;

public class InstantiationTracingBeanPostProcessor implements BeanPostProcessor {

    // 简单地返回实例化的bean
    public Object postProcessBeforeInitialization(Object bean,
            String beanName) throws BeansException {
        return bean; // 我们可能会在这里返回任何对象引用...
    }

    public Object postProcessAfterInitialization(Object bean,
            String beanName) throws BeansException {
        System.out.println("Bean '" + beanName + "' created : " + bean.toString());
        return bean;
    }

}
```
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:lang="http://www.springframework.org/schema/lang"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/lang
        http://www.springframework.org/schema/lang/spring-lang.xsd">

    <lang:groovy id="messenger"
            script-source="classpath:org/springframework/scripting/groovy/Messenger.groovy">
        <lang:property name="message" value="Fiona Apple Is Just So Dreamy."/>
    </lang:groovy>

    <!--
    当上面的bean（messenger）被实例化，这个自定义  BeanPostProcessor实现将事实输出到系统控制台
    -->
    <bean class="scripting.InstantiationTracingBeanPostProcessor"/>

</beans>
```
注意InstantiationTracingBeanPostProcessor怎么被简单的定义。它甚至没有一个名字，因为它是一个bean，它可以像任何其他bean一样被依赖注入。（上述配置还定义了一个由Groovy脚本支持的bean，Spring动态语言支持在[第35章“动态语言支持”](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#dynamic-language)一章中详细介绍。

以下简单的Java应用程序执行上述代码和配置：
```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.scripting.Messenger;

public final class Boot {

    public static void main(final String[] args) throws Exception {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("scripting/beans.xml");
        Messenger messenger = (Messenger) ctx.getBean("messenger");
        System.out.println(messenger);
    }

}
```

上述应用程序的输出类似于以下内容：
```
Bean 'messenger' created : org.springframework.scripting.groovy.GroovyMessenger@272961
org.springframework.scripting.groovy.GroovyMessenger@272961
```

### 示例： RequiredAnnotationBeanPostProcessor
将回调接口或注解与自定义BeanPostProcessor结合使用是扩展Spring IoC容器的常用手段。一个例子是Spring的RequiredAnnotationBeanPostProcessor - 一个Spring分发版附带的BeanPostProcessor实现，它确保被（任意）注解标记的bean上的JavaBean属性被实际（配置要）依赖注入一个值。

## 使用BeanFactoryPostProcessor自定义配置元数据
***
我们将看到的下一个扩展点是**org.springframework.beans.factory.config.BeanFactoryPostProcessor**。该接口的语义与**BeanPostProcessor**类似，主要区别在于：**BeanFactoryPostProcessor**对bean配置元数据进行操作;也就是说，Spring IoC容器允许**BeanFactoryPostProcessor**读取配置元数据，并可能在容器实例化除**BeanFactoryPostProcessors**之外的任何Bean之前,会更改它。

你可以配置多个**BeanFactoryPostProcessors**，并且可以通过设置order属性来控制这些BeanFactoryPostProcessors执行的顺序。但是，你只能在BeanFactoryPostProcessor实现了Ordered接口，才能设置此属性。如果你编写自己的BeanFactoryPostProcessor，你也应该考虑实现Ordered接口。有关更多详细信息，请参阅BeanFactoryPostProcessor和Ordered接口的javadocs。

> 如果要更改实际的bean实例（即从配置元数据创建的对象），则需要使用BeanPostProcessor（如第8.1节“使用BeanPostProcessor自定义bean”中所述）。尽管在技术上可以在BeanFactoryPostProcessor中使用bean实例（例如，使用BeanFactory.getBean()），但这样做会导致过早的bean实例化，违反了标准容器生命周期。这可能会导致负面的副作用，如旁路bean后处理。此外，BeanFactoryPostProcessors作用域在每个容器范围。这只有在使用容器层次结构时才有用。如果在一个容器中定义了一个BeanFactoryPostProcessor，那么它只会应用于该容器中的bean定义。一个容器中的Bean定义不会被另一个容器中的BeanFactoryPostProcessors进行post-processed，即使这两个容器都是同一层次结构的一部分。

当在ApplicationContext中声明一个BeanFactoryPostProcessor，它会被自动执行以便对定义容器的配置元数据进行更改。Spring包括一些预定义的BeanFactoryPostProcessor，如**PropertyOverrideConfigurer**和**PropertyPlaceholderConfigurer**。例如，也可以使用自定义BeanFactoryPostProcessor来注册自定义属性编辑器。

**ApplicationContext**自动检测部署到其中的任何实现**BeanFactoryPostProcessor**接口的bean。它在适当的时候使用这些bean作为**BeanFactoryPostProcessor**。你可以像任何其他bean一样部署这些后处理器bean。

> 与BeanPostProcessors一样，您通常不想将BeanFactoryPostProcessors配置为懒加载。如果没有其他bean引用一个Bean(Factory)PostProcessor，那么后处理器根本不会被实例化。因此，它的懒加载标记会被忽略，即使在&lt;beans/>元素的声明中将default-lazy-init属性设置为true，Bean(Factory)PostProcessor也会被立即实例化。

### 示例：类名替换PropertyPlaceholderConfigurer
你可以使用**PropertyPlaceholderConfigurer **在在一个单独的文件使用标准的Java Properties格式在bean定义具化属性值。这样做使得部署应用程序的人可以自定义环境特定的属性（如数据库URL和密码），避免修改容器的主要XML定义文件或文件的复杂性及风险。

考虑以下基于XML的配置元数据片段，其中定义了带有占位符值的DataSource。该示例显示从外部Properties文件配置的属性。在运行时，PropertyPlaceholderConfigurer应用于将替换DataSource的某些属性的元数据。被替换的值被指定为遵循 Ant / log4j / JSP EL风格的${property-name}形式的占位符。
```xml
<bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
    <property name="locations" value="classpath:com/foo/jdbc.properties"/>
</bean>

<bean id="dataSource" destroy-method="close"
        class="org.apache.commons.dbcp.BasicDataSource">
    <property name="driverClassName" value="${jdbc.driverClassName}"/>
    <property name="url" value="${jdbc.url}"/>
    <property name="username" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>
```
实际值来自另一个文件，以标准Java Properties格式：
```
jdbc.driverClassName=org.hsqldb.jdbcDriver
jdbc.url=jdbc:hsqldb:hsql://production:9002
jdbc.username=sa
jdbc.password=root
```
因此，字符串$ {jdbc.username}在运行时被替换为值“sa”，同样适用于与属性文件中的键匹配的其他占位符值。PropertyPlaceholderConfigurer在一个bean中多数properties和attributes中检查占位符。此外，可以定制占位符前缀和后缀。

使用Spring 2.5引入的context 命名空间，可以使用专用配置元素配置属性占位符。一个或多个位置可以在location属性中以用逗号分隔列表提供。
```xml
<context:property-placeholder location="classpath:com/foo/jdbc.properties"/>
```
PropertyPlaceholderConfigurer不仅在您指定的Properties文件中查找属性。默认如果它没有在指定的属性文件中找到一个属性，它还将检查Java系统属性。你可以通过将configurer的systemPropertiesMode属性设置为以下三个支持的整数值之一来自定义此行为：
- 从不（0）：从不检查系统属性
- 回退（1）：如果在指定的属性文件中没有可解析的，则检查系统属性。这个是默认设置
- 覆盖（2）：首先检查系统属性，然后再尝试指定的属性文件。这允许系统属性覆盖任何其他属性源。

有关详细信息，请参阅PropertyPlaceholderConfigurer javadocs。
> 您可以使用PropertyPlaceholderConfigurer来替换类名，这在运行时必须选择特定的实现类时有时很有用。例如：
```xml
<bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
    <property name="locations">
        <value>classpath:com/foo/strategy.properties</value>
    </property>
    <property name="properties">
        <value>custom.strategy.class=com.foo.DefaultStrategy</value>
    </property>
</bean>

<bean id="serviceStrategy" class="${custom.strategy.class}"/>
```
如果类不能在运行时解析为有效类，那么即将要创建的bean的解析失败了，对于非懒加载的bean这发生ApplicationContext的preInstantiateSingletons()阶段期间。

### 示例：PropertyOverrideConfigurer
另一个bean工厂后处理器的**PropertyOverrideConfigurer**与**PropertyPlaceholderConfigurer**类似，但是不像后者，原始定义可以具有默认值或根本没有任何值用于bean属性。如果一个用于覆盖的Properties文件没有某个bean属性的条目，则使用默认上下文定义。

请注意，bean定义感知不到被覆盖，所以从XML定义文件中不能立即显示覆盖configurer正在被使用。如果多个PropertyOverrideConfigurer实例为同一个bean属性定义不同的值，由于覆盖机制而获胜，最后一个覆盖值生效。

属性文件配置行采用以下格式：
```
beanName.property=value
```
例如：
```
dataSource.driverClassName=com.mysql.jdbc.Driver
dataSource.url=jdbc:mysql:mydb
```
此示例文件可以与包含称为dataSource的bean的容器定义一起使用，这个bean有一个driver和url属性。

还支持复合属性名称，只要除了要被覆盖的最终属性外，路径的每个组件都已经是非null（可能由构造函数初始化）。在这个例子中...
```
foo.fred.bob.sammy=123
```
foo bean的fred属性的bob属性的sammy属性设置为标量值123。

> 指定的覆盖值始终为字面值;它们不会被转换为bean引用。当XML bean定义中的原始值指定一个bean引用时，此约定也适用。

使用Spring 2.5引入的context命名空间时，可以使用专用配置元素配置属性覆盖：
```xml
<context:property-override location="classpath:override.properties"/>
```

## 使用FactoryBean自定义实例化逻辑
***
为自己的工厂的对象实现org.springframework.beans.factory.FactoryBean接口。

FactoryBean接口是Spring IoC容器的实例化逻辑的可插入点。如果你有复杂的初始化代码，那么它可以在Java中更好的表达，而不是在一堆（可能）冗长的XML中，你可以创建自己的FactoryBean，在该类中编写复杂的初始化，然后将你的自定义FactoryBean插入容器。

FactoryBean接口提供三种方法：
- **Object getObject()**: 返回此工厂创建的对象的实例。该实例可能是共享的，这取决于这个工厂是否返回单例或原型。
- **boolean isSingleton()**: 如果此FactoryBean返回单例则返回true，否则返回false。
- **Class getObjectType()**: 返回getObject()方法返回的对象类型，如果类型未提前知道，则返回null。

FactoryBean的概念和接口在Spring Framework中的许多地方使用; FactoryBean接口的50多个实现与Spring本身一起运行。

当你需要向容器询问实际的FactoryBean实例本身而不是其生成的bean时，在调用ApplicationContext的getBean()方法时，使用＆符号（＆）来表示bean的id。所以对于给定的FactoryBean，其ID为myBean，在容器上调用getBean(“myBean”)返回FactoryBean的产物;而调用getBean(“＆myBean”)则返回FactoryBean实例本身。

***
# 基于注解的容器配置
***
<div style="border: 1px solid #ccc;background-color:#f8f8f8;padding:20px;">**注解比XML更适合配置Spring吗？**      
基于注解配置的引入产生了这样一个问题，这种方式比XML更好吗？短的答案是看情况。长的答案是每种方式都有它自己的优缺点，并且通常由开发人员决定哪种策略更适合他们。由于它们被定义的方式，注解在它们的声明中提供了大量的上下文，从而使配置更短，更简洁。然而，XML非常适合在不接触源代码或重新编译组件的情况下组装组件。一些开发人员喜欢将装配就近源代码，而其他开发者认为注解类不再是POJO，此外，配置变得分散化，难以控制。      
无论怎么选择，Spring都可以容纳这两种风格，甚至将它们混合在一起。需要指出的是，通过其JavaConfig选项，Spring允许以非侵入式的方式使用注解，而不用触及目标组件源代码，并且而在工具方面，Spring Tool Suite支持所有配置样式。
</div> 
基于注解的配置提供了XML配置的替代方法，这种配置依赖于字节码元数据来装配组件，而不是角括号声明。相比使用XML来声明一个bean装配，开发者通过在相关类，方法，字段声明上使用注解，将配置移动到组件类本身。正如在“示例：RequiredAnnotationBeanPostProcessor”一节中所提到的，使用** BeanPostProcessor** 结合注解是扩展Spring IoC容器的常用手段。例如，Spring 2.0引入了使用@Required注解强制执行所需属性的可能。Spring 2.5使得可以遵循相同的通用方式来驱动Spring的依赖注入。实质上，@Autowired注解提供了与第4.5节 [“自动装配协作者”](#自动装配协作者) 中所述相同的功能，但具有更细粒度的控制和更广泛的适用性。Spring 2.5还添加了对JSR-250注解的支持，如**@PostConstruct**和**@PreDestroy**。Spring 3.0增加了javax.inject包中包含的JSR-330（用于Java的依赖注入）注解，例如**@Inject**和**@Named**。有关这些注解的详细信息，请参见 [相关章节]()。

> 注解注入在XML注入之前执行，因此通过这两种方式进行注入时，后一种配置会覆盖前面的属性装配。

和之前一样，你可以以单独的bean定义来注册，但也可以通过在基于XML的Spring配置中包含以下标签来隐式注册（注意包含的context命名空间）
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```
（隐式注册的post-processors包括**AutowiredAnnotationBeanPostProcessor**，**CommonAnnotationBeanPostProcessor**，**PersistenceAnnotationBeanPostProcessor**以及前述的**RequiredAnnotationBeanPostProcessor**。）

>  **&lt;context:annotation-config/>**只在定义它们的应用程序上下文中查找bean上的注解。这意味着，如果你将**&lt;context:annotation-config/>**放入到一个**WebApplicationContext**用于**DispatcherServlet**，那么它只会检查controller中的**@AutoWired** bean，而不是service上。更多信息请查看第22.2节 [“The 于DispatcherServlet”](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#mvc-servlet)。

## @Required
***
@Required注解应用于bean属性的setter方法，如下面示例所示`：
```java
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Required
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // ...

}
```
这个注解只是的指示受影响的bean属性必须在配置时填充，通过在bean定义中的明确属性值或自动装配。如果受影响bean属性没有被填充，容器会抛出一个异常；这允许急切和明确的失败，在以后避免NullPointerExceptions等异常。它还建议您将断言放入bean类本身，例如，将其放入init方法中。这样做确保这些必须的引用和值，即使你在容器外使用这个类。

##  @Autowired
***
> 以下示例中可以使用JSR 330的@Inject注解来代替Spring的@Autowired注解。请参阅 [这里]() 了解更多详情。

你可以将@Autowired注解应用到构造函数：
```java
public class MovieRecommender {

    private final CustomerPreferenceDao customerPreferenceDao;

    @Autowired
    public MovieRecommender(CustomerPreferenceDao customerPreferenceDao) {
        this.customerPreferenceDao = customerPreferenceDao;
    }

    // ...

}
```
> 从Spring Framework 4.3开始，如果目标bean只定义一个构造函数，则@Autowired构造函数不再需要。如果有几个构造函数可用，至少必须注解一个构造函数来指导容器必须使用哪个构造函数。

如预期的那样，您还可以将@Autowired注解应用于“传统”setter方法：
```java
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Autowired
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // ...

}
```
您还可以将注解应用于具有任意名称和/或多个参数的方法：
```java
public class MovieRecommender {

    private MovieCatalog movieCatalog;

    private CustomerPreferenceDao customerPreferenceDao;

    @Autowired
    public void prepare(MovieCatalog movieCatalog,
            CustomerPreferenceDao customerPreferenceDao) {
        this.movieCatalog = movieCatalog;
        this.customerPreferenceDao = customerPreferenceDao;
    }

    // ...

}
```
您也可以将@Autowired应用于字段，甚至将其与构造函数进行混合：
```java
public class MovieRecommender {

    private final CustomerPreferenceDao customerPreferenceDao;

    @Autowired
    private MovieCatalog movieCatalog;

    @Autowired
    public MovieRecommender(CustomerPreferenceDao customerPreferenceDao) {
        this.customerPreferenceDao = customerPreferenceDao;
    }

    // ...

}
```
还可以通过将注解添加到期望特定类型数组的字段或方法上，从ApplicationContext提供该类型的所有bean。
```java
public class MovieRecommender {

    @Autowired
    private MovieCatalog[] movieCatalogs;

    // ...

}
```
同样适用于类型集合：
```java
public class MovieRecommender {

    private Set<MovieCatalog> movieCatalogs;

    @Autowired
    public void setMovieCatalogs(Set<MovieCatalog> movieCatalogs) {
        this.movieCatalogs = movieCatalogs;
    }

    // ...

}
```
> 如果你希望将数组或列表中的项目按特定顺序排序话，你的bean可以实现**org.springframework.core.Ordered**接口，或者要么使用**@Order**要么使用标准的**@Priority**。

只要预期的键的类型为String，即使类型Map也可以自动装配。 Map值将包含预期类型的​​所有bean，键将包含相应的bean名称：Map值将包含预期类型的​​所有bean，键将包含相应的bean名称：  
```java
public class MovieRecommender {

    private Map<String, MovieCatalog> movieCatalogs;

    @Autowired
    public void setMovieCatalogs(Map<String, MovieCatalog> movieCatalogs) {
        this.movieCatalogs = movieCatalogs;
    }

    // ...

}
```
默认情况下，自动装配会在没有候选bean可用时失败；默认行为是将注解的方法，构造函数和字段视为指明的必须依赖。这种行为可以如下演示来修改：
```java
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Autowired(required=false)
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // ...

}
```
> 每个类只有一个注解的构造函数可以被标记为required，但是可以注解多个构造函数为非必须。在这种情况下，每个被考虑在候选之中，并且Spring使用最贪婪（greediest）的构造函数，也就是有最多参数的构造函数，该构造函数的依赖将被满足。**@AutoWired的required属性建议使用@Required注解。**required属性表示该属性对于自动装配目的不是必需的，如果不能被自动装配，那么该属性将被忽略。另一方面，@Required更强大，它强迫属性必须被容器支持的任何bean设置，如果没有注入值，则会引发相应的异常。

你还可以对于众所周知的可解析的依赖接口使用@Autowired：**BeanFactory**, **ApplicationContext**, **Environment**,** ResourceLoader**, **ApplicationEventPublisher**, 和 **MessageSource**。这些接口以及它们的拓展接口（比如**ConfigurableApplicationContext**或**ResourcePatternResolver**）是自动解析的，不需要特殊设置。
```java
public class MovieRecommender {

    @Autowired
    private ApplicationContext context;

    public MovieRecommender() {
    }

    // ...

}
```
>  **@Autowired **， **@Inject **， **@Resource **和 **@Value **注解由Spring  **BeanPostProcessor **实现处理，这又意味着你不能在自己的 **BeanPostProcessor **或 **BeanFactoryPostProcessor **类型（如果有的话）中应用这些注解。这些类型必须通过XML或使用Spring @Bean方法显式地“装配”。

## 使用限定符微调基于注解的自动装配
***
因为通过类型的自动装配可能导致多个候选者，它经常需要对选择的过程有更多的控制。完成这项工作的一个方式是使用Spring的**@Primary**注解。**@Primary**表示当多个bean是自动装配到一个单值依赖的候选者时，给定一个特定的bean优先权。如果候选人中只存在一个“主要”bean，那么它将是自动装配的值。

让我们假设我们下面的配置，它定义firstMovieCatalog作为主要的MovieCatalog。
```java
@Configuration
public class MovieConfiguration {

    @Bean
    @Primary
    public MovieCatalog firstMovieCatalog() { ... }

    @Bean
    public MovieCatalog secondMovieCatalog() { ... }

    // ...

}
```
通过这样的配置，以下MovieRecommender将会使用firstMovieCatalog自动注解。
```java
public class MovieRecommender {

    @Autowired
    private MovieCatalog movieCatalog;

    // ...

}
```
相应的bean定义如下所示。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

    <bean class="example.SimpleMovieCatalog" primary="true">
        <!-- 注入此bean所需的任何依赖项 -->
    </bean>

    <bean class="example.SimpleMovieCatalog">
        <!-- 注入此bean所需的任何依赖项 -->
    </bean>

    <bean id="movieRecommender" class="example.MovieRecommender"/>

</beans>
```
## 使用@Qualifier微调基于注解的自动装配
***
当使用通过类型的注解，有多个实例匹配确定一个主要候选者时，**@Primary**是一种有效方式。当对选择过程需要更多控制时，可以使用Spring的**@Qualifier**注解。您可以将qualifier值与特定参数相关联，缩小类型匹配集，以便为每个参数选择特定的bean。在最简单的情况下，这可以是一个简单的描述性值：
```java
public class MovieRecommender {

    @Autowired
    @Qualifier("main")
    private MovieCatalog movieCatalog;

    // ...

}
```
**@Qualifier**注解也可以在各个构造函数参数或方法参数中指定：
```java
public class MovieRecommender {

    private MovieCatalog movieCatalog;

    private CustomerPreferenceDao customerPreferenceDao;

    @Autowired
    public void prepare(@Qualifier("main")MovieCatalog movieCatalog,
            CustomerPreferenceDao customerPreferenceDao) {
        this.movieCatalog = movieCatalog;
        this.customerPreferenceDao = customerPreferenceDao;
    }

    // ...

}
```
相应的bean定义如下所示。具有qualifier值“main”的bean，使用具有相同值的qualifier的构造函数参数进行装配。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

    <bean class="example.SimpleMovieCatalog">
        <qualifier value="main"/>

        <!-- 注入此bean所需的任何依赖项 -->
    </bean>

    <bean class="example.SimpleMovieCatalog">
        <qualifier value="action"/>

        <!-- 注入此bean所需的任何依赖项 -->
    </bean>

    <bean id="movieRecommender" class="example.MovieRecommender"/>

</beans>
```
对于回退匹配，bean名称被认为是默认qualifier值。因此，您可以使用id“main”来定义bean，而不是嵌套的qualifier元素，这导致相同的匹配结果。但是，尽管你可以使用这个惯例通过名称引用特定的bean，但是@Autowired从根本上是关于使用可选语义qualifier的类型驱动的注入。这意味着即使使用bean名称回退，qualifier值在类型匹配集合中总是具有缩小的语义;他们没有在语义上表达对唯一bean ID的引用。良好的qualifier值是“main”或“EMEA”或“persistent”，表示独立于bean id的特定组件的特征，可以在上述示例中的匿名bean定义的情况下自动生成。

Qualifiers也适用于上面讨论的类型集合，例如Set&lt;MovieCatalog>。在这个情况下，根据声明的qualifier所有匹配的bean作为集合被注入。这意味着qualifier不一定是唯一的;它们只是构成过滤的标准。例如，您可以使用相同的qualifier值“action”定义多个MovieCatalog bean，所有这些都将注入到使用@Qualifier(“action”)注解的Set&lt;MovieCatalog>中。

> 如果你打算使用按名称的注解驱动注入，那么不要主要使用@Autowired，即使在技术上能够通过@Qualifier值引用一个bean名称。而是使用JSR-250 @Resource注解，它通过它的唯一名称语义定义识别一个指定的目标组件，声明的类型和匹配过程无关。@Autowired有不同的语义：在按类型选择候选bean之后，指定的String限定符值只有在这些类型选择的候选者才会被考虑，例如，匹配一个“account”限定符与与标记有相同限定符标签的bean。对于被定义为collection/map或数组类型的bean，@Resource是一个更好的解决方法，通过唯一名称来引用指定的集合或数组bean。也就是说，从4.3开始，只要元素类型信息保存在@Bean返回类型签名或集合继承层次结构中，就可以通过Spring的@Autowired类型匹配算法来匹配collection/map和数组类型。在这种情况下，限定符值可用于在相同类型的集合中进行选择，如前一段所述。
从4.3开始，@Autowired还考虑注入自引用，即引用回目前注入的bean。注意自注入是一个回退；对其他组件的常规依赖性始终优先。在这个意义上，自我引用不参与正式的候选人选择，因此特别的从不会是primary;相反，他们总是作为最低的优先级。在实践中，仅使用自我引用只作为最后的手段。例如，通过bean的事务代理在同一个实例上调用其他方法：在这种情况下，考虑将受影响的方法分解为单独的委托bean。或者，使用@Resource，它可以通过其唯一的名称获取代理回到当前的bean。@Autowired适用于字段，构造函数和多参数方法，允许在参数级别缩小qualifier注解。相比之下，@Resource仅支持具有单个参数的字段和bean属性setter方法。因此，如果您的注入目标是构造函数或多参数方法，请坚持使用qualifier。

你可以创建你自己的自定义qualifier注解。简单定一个一个注解，并且在你的定义中提供@Qualifier注解：
```java
@Target({ElementType.FIELD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Genre {

    String value();
}
```
然后你可以在自动装配的字段和参数上提供这个自定义qualifier：
```java
public class MovieRecommender {

    @Autowired
    @Genre("Action")
    private MovieCatalog actionCatalog;
    private MovieCatalog comedyCatalog;

    @Autowired
    public void setComedyCatalog(@Genre("Comedy") MovieCatalog comedyCatalog) {
        this.comedyCatalog = comedyCatalog;
    }

    // ...

}
```
接下来，提供关于候选bean定义的信息。你可以添加&lt;qualifier/>标签作为&lt;bean/>标签的子元素，然后指定type和value来匹配你的自定义qualifier注解。type是匹配注解的完全限定类名。或者，如果没有名称冲突的风险存在，为了方便你也可以使用简短类名称。这两种方式都在下面的示例中演示。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

    <bean class="example.SimpleMovieCatalog">
        <qualifier type="Genre" value="Action"/>
        <!-- 注入此bean所需的任何依赖项 -->
    </bean>

    <bean class="example.SimpleMovieCatalog">
        <qualifier type="example.Genre" value="Comedy"/>
        <!-- 注入此bean所需的任何依赖项 -->
    </bean>

    <bean id="movieRecommender" class="example.MovieRecommender"/>

</beans>
```
在10节[“类路径扫描和托管组件”]()中，您将看到一个相比以XML格式提供qualifier元数据，基于注解的替代方法。具体来说，请参见第10.8节[“使用z注解提供qualifier元数据”]()。

在某些情况下，使用没有value的注解可能就足够了。当注解提供更通用的目的并且可以跨几种不同类型的依赖被应用时，这可能是有用的。例如，当没有Internet连接可用时将被搜索，你可能会提供一个离线目录。首先定义这个简单的注解：
```java
@Target({ElementType.FIELD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Offline {

}
```
然后添加这个注解到需要自动装配的字段或属性：
```java
public class MovieRecommender {

    @Autowired
    @Offline
    private MovieCatalog offlineCatalog;

    // ...

}
```
现在bean定义仅需要一个qualifier type：
``xml
<bean class="example.SimpleMovieCatalog">
    <qualifier type="Offline"/>
    <!-- 注入此bean所需的任何依赖项 -->
</bean>
```
你也可以定义接收命名属性自定义qualifier注解，取代这个简单的value属性。如果要在被自动装配的一个字段或参数上指定多个属性值，则bean应以必须与所有这些属性值相匹配才会被视为自动装配候选者。作为示例，考虑下面的注解定义：
```java
@Target({ElementType.FIELD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface MovieQualifier {

    String genre();

    Format format();

}
```
在这里Format是一个枚举：
```java
public enum Format {
    VHS, DVD, BLURAY
}
```
要自动装配的字段被用自定义qualifier注解，并且包含这两个属性值：genre和format。
```java
public class MovieRecommender {

    @Autowired
    @MovieQualifier(format=Format.VHS, genre="Action")
    private MovieCatalog actionVhsCatalog;

    @Autowired
    @MovieQualifier(format=Format.VHS, genre="Comedy")
    private MovieCatalog comedyVhsCatalog;

    @Autowired
    @MovieQualifier(format=Format.DVD, genre="Action")
    private MovieCatalog actionDvdCatalog;

    @Autowired
    @MovieQualifier(format=Format.BLURAY, genre="Comedy")
    private MovieCatalog comedyBluRayCatalog;

    // ...

}
```
最终，bean定义应该包含匹配的限定符值。这个示例还说明了可以使用bean meta属性替代&lt;qualifier/>子元素。如果可用，则&lt;qualifier/>及其属性优先，但如果不存在这样的qualifier，则自动装配机制将回退在&lt;meta/>标记中提供的值上，如以下示例中的最后两个bean定义。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

    <bean class="example.SimpleMovieCatalog">
        <qualifier type="MovieQualifier">
            <attribute key="format" value="VHS"/>
            <attribute key="genre" value="Action"/>
        </qualifier>
        <!-- 注入此bean所需的任何依赖项 -->
    </bean>

    <bean class="example.SimpleMovieCatalog">
        <qualifier type="MovieQualifier">
            <attribute key="format" value="VHS"/>
            <attribute key="genre" value="Comedy"/>
        </qualifier>
        <!-- 注入此bean所需的任何依赖项 -->
    </bean>

    <bean class="example.SimpleMovieCatalog">
        <meta key="format" value="DVD"/>
        <meta key="genre" value="Action"/>
        <!-- 注入此bean所需的任何依赖项 -->
    </bean>

    <bean class="example.SimpleMovieCatalog">
        <meta key="format" value="BLURAY"/>
        <meta key="genre" value="Comedy"/>
        <!-- 注入此bean所需的任何依赖项 -->
    </bean>

</beans>
```

## 使用泛型作为自动装配限定符
***
除了@Qualifier注解之外，还可以使用Java泛型类型作为隐式形式的限定。例如，假设你有以下配置：
```java
@Configuration
public class MyConfiguration {

    @Bean
    public StringStore stringStore() {
        return new StringStore();
    }

    @Bean
    public IntegerStore integerStore() {
        return new IntegerStore();
    }

}
```
假设上面的bean实现了泛型接口，即Store&lt;String>和Store&lt;Integer>, 你可以@Autowired这个Store接口并且泛型会被用作限定符：
```java
@Autowired
private Store<String> s1; // <String> qualifier,注入stringStore bean

@Autowired
private Store<Integer> s2; // <Integer> qualifier,注入integerStore bean
```
当自动装配List，Map和Array时，泛型qualifier同样使用：
```java
// 只要Store bean有一个 <Integer>泛型，就会注入所有的这样的Store bean
// Store<String> beans不会出现在这个list中
@Autowired
private List<Store<Integer>> s;
```

##  CustomAutowireConfigurer
***
CustomAutowireConfigurer是一个BeanFactoryPostProcessor，它可以使你注册你自己的自定义qualifier注解类型，即使它们没有使用Spring的@Qualifier注解进行注解。
```xml
<bean id="customAutowireConfigurer"
        class="org.springframework.beans.factory.annotation.CustomAutowireConfigurer">
    <property name="customQualifierTypes">
        <set>
            <value>example.CustomQualifier</value>
        </set>
    </property>
</bean>
```
AutowireCandidateResolver通过以下方式确定自动装配候选者：
- 每个bean定义的autowire-candidate值
- 然后在&lt;beans/>可用的default-autowire-candidates模式
- @Qualifier注解的存在以及使用CustomAutowireConfigurer注册的所有自定义注解。

当多个bean符合自动装配候选者时，决定哪一个为“primary”方式如下：如果候选人中只有一个bean定义的primary 属性设置为true，它会被选择。

## @Resource
**
Spring还支持使用JSR-250 @Resource注解对字段或bean属性的setter方法进行注入。这是Java EE 5和6中的常见模式，例如在JSF 1.2管理的bean或JAX-WS 2.0的端点中。Spring也对Spring管理的对象支持这种模式。

@Resource接收一个name属性，并且默认情况下，Spring将该值作为要注入的bean名称进行解释。换而言之，它遵循按名称语义，如下示例说明：
```java
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Resource(name="myMovieFinder")
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

}
```
如果没有明确指定name，则默认name来自字段名称或setter方法。在一个字段的情景下，它接收一个字段名称；在一个setter方法的情景下，它接收这个bean属性名称。所以下面示例是将名为“movieFinder”的bean注入到其setter方法中：
```java
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Resource
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

}
```
> 该注解提供的name通过CommonAnnotationBeanPostProcessor感知的ApplicationContext解析为一个bean名称。如果明确配置Spring的SimpleJndiBeanFactory，可以通过JNDI解析名称。但是，建议您依赖默认行为，只需使用Spring的JNDI查找功能来保留间接级别。

在@Resource使用没有指定明确的名称的情况下，类似与@Autowires，@Resource查找一个主类型匹配，而不是一个指定的命名bean，并解析众所周知的可解析依赖：BeanFactory，ApplicationContext，ResourceLoader，ApplicationEventPublisher和MessageSource接口。

因此在下面的示例中，customerPreferenceDao 字段首先查找一个命名为customerPreferenceDao的bean，然后回退到类型为CustomerPreferenceDao的主类型匹配。“context”字段是基于已知的可解析依赖类型ApplicationContext来注入的。
```java
public class MovieRecommender {

    @Resource
    private CustomerPreferenceDao customerPreferenceDao;

    @Resource
    private ApplicationContext context;

    public MovieRecommender() {
    }

    // ...

}
```
## @PostConstruct和@PreDestroy
***
**CommonAnnotationBeanPostProcessor**不仅可以识别**@Resource**注解，还可以识别JSR-250生命周期注解。在Spring 2.5中引入，对这些注解的支持提供了在 [初始化回调](#初始化回调)和[ 销毁回调](#销毁回调)中描述的另一种替代方法。只要CommonAnnotationBeanPostProcessor在Spring ApplicationContext中注册，带有这些注解之一的方法，是在生命周期中与响应的Spring生命周期接口方法或明确声明的回调方法相同的时间点调用。在下面示例，缓存将在初始化时预先填充，并在销毁后清除。
```java
public class CachingMovieLister {

    @PostConstruct
    public void populateMovieCache() {
        // 在初始化时填充电影缓存...
    }

    @PreDestroy
    public void clearMovieCache() {
        // 在销毁时清除电影缓存...
    }

}
```

> 有关组合各种生命周期机制的影响的详细信息，请参阅[“组合生命周期机制”](#组合生命周期机制)一节。

***
# 类路径扫描和管理的组件
***
本章中的大多数示例使用XML指定在Spring容器内生成每个BeanDifinition的配置元数据。前一节（第9节“基于注释的容器配置”）演示了如何通过源代码级注解提供大量配置元数据。然而，即使在这些例子中，“基本”bean定义也是在XML文件中明确定义的，而注解只是用来驱动依赖注入。本节介绍通过扫描类路径隐式检测候选组件的选项。候选组件是与过滤器条件匹配并且在容器内有对应的bean定义注册。这消除了使用XML来执行bean注册的需要;作为代替，你可以使用注解（例如@Component），AspectJ类型表达式，或者你自己的自定义过滤条件来选择哪些类会有在容器里的bean定义注册。

> 从Spring 3.0开始，Spring JavaConfig项目提供的许多功能都是核心Spring框架的一部分。这允许你使用Java而不是传统的XML文件定义bean。有关如何使用这些新功能的示例，请查看@Configuration，@Bean，@Import和@DependsOn注释。

## @Component和更多模型注解
***
@Repository注解是任何满足存储库（也称为数据访问对象或DAO）角色或原型的类的标记。该标记的用途是自动翻译异常，如第20.2.2节[“异常翻译”](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#orm-exception-translation)所述。

Spring提供了更多的模型注解：@Componect，@Service和@Controller。@Component是对任何Spring管理的组件的通用模型。@Repository，@Service和@Controller是针对更具体使用场景的专用化@Component例如，分别用于持久化，服务和表示层中。因此，你可以使用@Component对组件类进行注解，但通过使用@Repository，@Service或@Controller注解它们，你的类更适合于通过工具或相关方面进行处理。例如，这些模型注解是切面点的理想目标。还有可能@Repository，@Service和@Controller可能会在未来的Spring框架发布版中带来额外的语义。因此，如果你在你的服务层在@Component和@Service中选择，@Service显然是更好的选择。类似的，如上所述，@Repository已被支持作为持久层中自动异常转换的标记。

## 元注解
***
许多Spring提供的注解可以在你自己的代码中用作元注解。元注解只是可以应用于其他注解的注解。例如，上面提到的@Service注解是使用@Componect进行元注解的：
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component // Spring will see this and treat @Service in the same way as @Component
public @interface Service {

    // ....
}
```
元注解也可以组合起来创建组合注解。例如，Spring MVC中的@RestController注解由@Controller和@ResponseBody组成。

此外，组合注解可以可选地从元注解重新声明属性以允许用户自定义。当你只想暴露这个元注解属性的子集的时候，这特别有用。例如，Spring的@SessionScope注解将域名称硬编码为session，但人允许定制proxyMode。
```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Scope(WebApplicationContext.SCOPE_SESSION)
public @interface SessionScope {

    /**
     * Alias for {@link Scope#proxyMode}.
     * <p>Defaults to {@link ScopedProxyMode#TARGET_CLASS}.
     */
    @AliasFor(annotation = Scope.class)
    ScopedProxyMode proxyMode() default ScopedProxyMode.TARGET_CLASS;

}
```
然后可以使用@SessionScope而不声明proxyMode，如下所示：
```java
@Service
@SessionScope
public class SessionScopedService {
    // ...
}
```
或对proxyMode使用一个覆盖值，如下：
```java
@Service
@SessionScope(proxyMode = ScopedProxyMode.INTERFACES)
public class SessionScopedUserService implements UserService {
    // ...
}
```
有关更多详细信息，请参阅[Spring 注解编程模型](#http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#annotation-programming-model)。


## 自动检测类和注册bean定义
***
Spring可以自动探测模型类并且在ApplicationContext中注册相应的BeanDefinition。如下，以下两个类有资格进行这种自动检测：
```java
@Service
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Autowired
    public SimpleMovieLister(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

}
```
```java
@Repository
public class JpaMovieFinder implements MovieFinder {
    // implementation elided for clarity
}
```
要自动探测这些类并且注册相应的bean，你需要添加@ComponentScan到你的@Configuration类，其中basePackages属性是两个类的公共父包。（或者，你可以指定包含每个类的父包的逗号/分号/空格分隔列表。）
```java
@Configuration
@ComponentScan(basePackages = "org.example")
public class AppConfig  {
    ...
}
```

> 为了简洁，上述可能已经使用了注解的value属性，即@ComponentScan（“org.example”）

以下是使用XML
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="org.example"/>

</beans>
```

> 使用&lt;context:component-scan>隐式启用&lt;context:annotation-config>的功能。当使用&lt;context:component-scan>时通常不需要包含&lt;context:annotation-config>元素。

> classpath包的扫描需要在类路径中存在相应的目录条目。使用Ant构建JAR时，请确保不激活JAR任务的仅文件切换。此外，classpath目录基于安全策略可能不会在某些环境中暴露出来，例如JDK 1.7.0_45及更高版本的独立app（这需要您的清单中的“受信任的库”设置;请参阅http://stackoverflow.com/questions/19394570/java-jre-7u45-breaks-classloader-getresources）。

此外，当您使用component-scan元素时，AutowiredAnnotationBeanPostProcessor和CommonAnnotationBeanPostProcessor都将被隐式包含。这意味着这两个组件是一起被自动检测和装配的，它们都都没有XML中提供的任何bean配置元数据。

> 您可以通过包含值为false的annotation-config属性来禁用AutowiredAnnotationBeanPostProcessor和CommonAnnotationBeanPostProcessor的注册。

## 使用过滤器来自定义扫描
***
默认情况下，使用@Component，@Repository，@Service，@Controller，或其自身用@Component注解的自定义注解，注解的类是唯一被检测到的候选组件。然而，你只需要通过应用自定义过滤器就可以修改和拓展这个行为。将它们添加为@ComponentScan注解的includeFilters或excludeFilters参数（或者作为component-scan元素的include-filter或exclude-filter子元素）。每个过滤器元素需要type和expression属性。下面表格描述了过滤选项。

|过滤器type|expression示例|描述|
|-------------------|------------------|-------|
|annotation(默认)|org.example.SomeAnnotation|存在于目标组件上的注解类型级别
|assignable|org.example.SomeClass|目标组件被分配（拓展/实现）的类（或接口）.
|aspectj|org.example..*Service+|匹配目标组件的AspectJ类型表达式。
|regex|org\.example\.Default.*|匹配目标组件类名称的正则表达式。
|custom|org.example.MyTypeFilter|org.springframework.core.type.TypeFilter接口的自定义实现。

以下示例显示忽略所有@Repository注解，而使用“stub”repositories的配置。
```java
@Configuration
@ComponentScan(basePackages = "org.example",
        includeFilters = @Filter(type = FilterType.REGEX, pattern = ".*Stub.*Repository"),
        excludeFilters = @Filter(Repository.class))
public class AppConfig {
    ...
}
```
等同于使用XML
```xml
<beans>
    <context:component-scan base-package="org.example">
        <context:include-filter type="regex"
                expression=".*Stub.*Repository"/>
        <context:exclude-filter type="annotation"
                expression="org.springframework.stereotype.Repository"/>
    </context:component-scan>
</beans>
```
> 你还可以通过在注解上设置useDefaultFilters=false或将use-default-filters=“false”作为&lt;component-scan/>元素的属性来禁用默认过滤器。这将实际上禁用使用@Component，@Repository，@Service，@Controller或@Configuration注解的类的自动检测。

## 在组件中定义bean元数据
***
Spring组件还可以向容器提供bean定义元数据。你可以使用与@Configuration注解类中定义bean元数据相同的@Bean注解来执行此操作。这有一个简单示例：
```java
@Component
public class FactoryMethodComponent {

    @Bean
    @Qualifier("public")
    public TestBean publicInstance() {
        return new TestBean("publicInstance");
    }

    public void doWork() {
        // 组件方法实现忽略...
    }

}
```
这个类是一个Spring组件，它的doWork()方法中包含特定于应用程序的代码。但是，它还提供了一个bean定义，它有一个工厂方法publicInstance()。这个@Bean注解标识工厂方法和其他bean定义属性，例如通过@Qualifier注解设置限定符值。可以指定的其他方法级注解是@Scope，@Lazy和自定义限定符注解。

> 除了组件初始化的角色之外，@Lazy注解也可以放置在标有@Autowired或@Inject的注入点上。在这种情况下，它导致注入了一个懒解析代理。

如前面讨论的也支持自动装配的字段和方法，它有对@Bean方法的自动装配的额外支持：
```java
@Component
public class FactoryMethodComponent {

    private static int i;

    @Bean
    @Qualifier("public")
    public TestBean publicInstance() {
        return new TestBean("publicInstance");
    }

    // 使用自定义限定符和方法参数的自动装配
    @Bean
    protected TestBean protectedInstance(
            @Qualifier("public") TestBean spouse,
            @Value("#{privateInstance.age}") String country) {
        TestBean tb = new TestBean("protectedInstance", 1);
        tb.setSpouse(spouse);
        tb.setCountry(country);
        return tb;
    }

    @Bean
    private TestBean privateInstance() {
        return new TestBean("privateInstance", i++);
    }

    @Bean
    @RequestScope
    public TestBean requestScopedInstance() {
        return new TestBean("requestScopedInstance", 3);
    }

}
```
该示例自动将String方法参数country装配为另一个名为privateInstance的bean的Age属性的值。Spring表达式语言元素通过符号#{&lt;expression>}定义属性的值。对于@Value注解，一个表达式解析器在解析表达式文本的时候被预配置来查找bean名称。

从Spring框架4.3开始，你还可以声明InjectionPoint类型的工厂方法参数（或其更具体的子类DependencyDescriptor），以访问触发创建当前bean的请求注入点。请注意，这仅适用于实际bean实例的创建，而不适用于注入现有实例。因此，这个功能对于原型域bean最有意义。对于其他域，工厂方法只会看到在给定域内触发一个新的bean实例创建的注入点：例如，触发创建一个懒加载单例bean的依赖。在这种情况下，使用提供的注入点元数据进行语义关注。
```java
@Component
public class FactoryMethodComponent {

    @Bean @Scope("prototype")
    public TestBean prototypeInstance(InjectionPoint injectionPoint) {
        return new TestBean("prototypeInstance for " + injectionPoint.getMember());
    }
}
```
常规Spring组件中的@Bean方法的处理方式和它们在Spring @Configuration类中的对应方法不同。不同之处在于，@Component类没有使用CGLIB来增强，以拦截方法和字段的调用。CGLIB代理是@​​Configuration类中的@Bean方法中调用方法或字段的方式，它们为协作对象创建bean元数据引用;这样的方法不是使用普通的Java语义来调用的，而是通过容器来调用，为了能够提供通常的生命周期管理和Spring bean代理，即使通过对@Bean方法编程调用来引用其他bean。相反，在普通的@Component类中调用@Bean方法中的方法或字段具有标准Java语义，没有特殊的CGLIB处理或其他限制。

> 您可以将@Bean方法声明为static，允许调用它们，而无需创建它们包含的配置类作为实例。当定义后处理器时这尤其有意义，例如BeanFactoryPostProcessor和BeanPostProcessor类型，因为这样的bean将在容器生命周期早期初始化，并且应该避免在此时触发配置的其他部分。请注意，静态@Bean方法的调用永远不会被容器拦截，即使在@Configuration类中（见上文）。这是由于技术限制：CGLIB子类化只能覆盖非静态方法。因此，直接调用另一个@Bean方法将具有标准的Java语义，导致独立的实例直接从工厂方法返回。@Bean方法的Java语言可见性对Spring容器中生成的bean定义并没有立即的影响。您可以在非@Configuration类中以及任何地方的静态方法上自由声明您的工厂方法。但是，@Configuration类中的常规@Bean方法必须是可覆盖的，即不能将其声明为private或final。    
@Bean方法也将在给定组件或配置类的基类上发现，以及由组件或配置类实现的接口中声明的Java 8 default 方法。这允许在组成复杂的配置布局上有很大的灵活性，甚至可以通过Java 8 default方法（从Spring 4.2起）实现多重继承。    
最后，请注意，单个类可以为同一个bean持有多个@Bean方法，作为多个工厂方法的安排，以在运行根据可用依赖来使用。这与在其他配置方案中选择“greediest”构造函数或工厂方法的算法相同：具有最大数量的可满足依赖关系的变体将在构建时采用，类似于容器在多个@Autowired构造函数之间进行选择

## 命名自动检测的组件
***
当组件作为扫描过程的一部分自动检测时，其bean名称由该扫描器已知的BeanNameGenerator策略生成。默认情况下，任何Spring模型注解（@Component，@Repository，@Service和@Controller）包含一个name value，从而将该名称提供给相应的bean定义。

如果一个不包含名称value的注解或任何其他检测到的组件（例如由自定义过滤器发现的组件），默认的bean名称生成器返回小写形式，非完全限定的类名称。例如，如果检测到以下两个组件，则名称将为myMovieLister和movieFinderImpl：
```java
@Service("myMovieLister")
public class SimpleMovieLister {
    // ...
}
```
```java
@Repository
public class MovieFinderImpl implements MovieFinder {
    // ...
}
```

> 如果您不想依赖于默认的bean命名策略，你可以提供自定义bean命名策略。首先，实现BeanNameGenerator接口，并确保包含一个默认的无参构造函数。然后，在配置扫描器时提供完全限定的类名称：

```java
@Configuration
@ComponentScan(basePackages = "org.example", nameGenerator = MyNameGenerator.class)
public class AppConfig {
    ...
}
```
```xml
<beans>
    <context:component-scan base-package="org.example"
        name-generator="org.example.MyNameGenerator" />
</beans>
```
作为通用规则，只要当其他组件可能会明确引用时，考虑使用注解指定组件名称。另一方面，只要容器负责装配，自动生成的名称就足够了。

## 为自动检测的组件提供域
***
与Spring管理的组件一样，自动检测组件的默认和最常见的域是singleton。但是，有时你需要一个不同的域，这时你可以通过@Scope注解来制定。只要在注解重提供域名称：
```java
@Scope("prototype")
@Repository
public class MovieFinderImpl implements MovieFinder {
    // ...
}
```
有关Web特定域的详细信息，请参见第5.4节[“Request, session, global session, application和 WebSocket域”](#Request, session, global session, application和 WebSocket域)。

> 要为域解析提供自定义策略，而不是依赖基于注解的方式，请实现ScopeMetadataResolver接口，并确保包含一个默认的无参构造函数。然后，在配置扫描器时提供完全限定的类名称：
```java
@Configuration
@ComponentScan(basePackages = "org.example", scopeResolver = MyScopeResolver.class)
public class AppConfig {
    ...
}
```
```xml
<beans>
    <context:component-scan base-package="org.example"
            scope-resolver="org.example.MyScopeResolver" />
</beans>
```
当使用某些非单例域时，可能需要为作用域对象生成代理。原因在[“作用域bean作为依赖”](#作用域bean作为依赖)一节中已经描述。为此，scoped-proxy属性可以在component-scan元素中使用。三个可能的值是：no，interfaces和targetClass。例如，以下配置将导致标准的JDK动态代理：
```java
@Configuration
@ComponentScan(basePackages = "org.example", scopedProxy = ScopedProxyMode.INTERFACES)
public class AppConfig {
    ...
}
```
```xml
<beans>
    <context:component-scan base-package="org.example"
        scoped-proxy="interfaces" />
</beans>
```

## 使用注解提供限定符元数据
***
@Qualifier注解已经在第7.9.4节[“使用限定符微调基于注解的自动装配”](#使用限定符微调基于注解的自动装配)。该部分中的示例演示了@Qualifier注解的使用以及在你解析自动装配候选者时使用自定义限定符注解来提供更细粒度的控制。因为这些示例基于XML bean定义，所以使用XML中的bean元素的qualifier或meta子元素在候选bean定义上提供了限定符元数据。当依赖类路径扫描来自动检测组件时，你可以在候选类上使用那个类型级别注解来提供限定符元数据。以下三个例子说明了这种技术：
```java
@Component
@Qualifier("Action")
public class ActionMovieCatalog implements MovieCatalog {
    // ...
}
```
```java
@Component
@Genre("Action")
public class ActionMovieCatalog implements MovieCatalog {
    // ...
}
```
```java
@Component
@Offline
public class CachingMovieCatalog implements MovieCatalog {
    // ...
}
```
> 与大多数基于注解的替代方案一样，，请注意，注解元数据绑定到类定义本身，而使用XML允许同一类型的多个bean提供他们限定符元数据的变体，因为元数据是为每个实例，而不是每个类提供的。

***
# 使用JSR 330标准注解
***

***
# 基于Java的容器配置
***





