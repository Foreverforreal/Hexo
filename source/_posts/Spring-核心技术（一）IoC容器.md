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
|class|[“实例化bean”](#实例化bean)
|name|[“命名bean”](#命名bean)
|scope|Section 7.5, “Bean scopes”
|constructor arguments|Section 7.4.1, “Dependency Injection”
|properties|Section 7.4.1, “Dependency Injection”
|autowiring mode|Section 7.4.5, “Autowiring collaborators”
|lazy-initialization mode|Section 7.4.4, “Lazy-initialized beans”
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
类似于通过静态工厂方法实例化，也可以使用一个实例工厂方法，调用容器中现有的bean的非静态方法来创建一个新的bean。要使用这种机制，让class属性为空，并且在 **factory-bean** 属性中指定当前（或父/祖）容器中包含了要用来调用用于创建对象的实例方法的bean名称。使用 **factory-method** 属性着着工厂方法本身名称。
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
由于可以混合使用基于构造函数和基于setter的DI，为强制依赖使用构造函数，并且为可选依赖使用setter方法或配置方法是一个很好的经验法则。请注意，可以在setter方法上使用的的@Required注解可以使属性成为必需的依赖。<br> <br> 
Spring团队通常主张构造函数注入，因为它可以实现应用程序组件为不可变对象，并确保所需的依赖关系不为null。此外，构造函数注入的组件总是以完全初始化的状态返回给客户端（调用）代码。作为一个附注，大量的构造函数参数是一个不好的代码气息，这意味着该类可能有太多的责任，应该重构以更好地解决问题的正确分离。<br> <br> 
Setter注入应主要用于可选依赖，可以在类中分配合理的默认值。否则，必须在代码任何使用依赖的地方执行非空检查。setter注入的一个好处是setter方法使得该类的对象可以在以后重新配置或重新注入。因此对于setter注入，通过 JMX MBeans管理是一个引人注目的用例。<br> <br> 
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
一个内部bean定义不需要定义的id或name;如果指定的话，容器也不使用这样的值作为标识符。容器也会忽略创建的scope标志：内部bean总是匿名的，并且它们总是虽外部bean而创建。不可能将注入内部bean到除闭包bean外的其他协作bean中，也不可能独立访问它们。

作为一个个别案例，有可能从一个自定义域中接收销毁回调，例如，对一个包含在单例bean中的request-scoped内部bean：内部Bean实例的创建将绑定到其包含的bean上但是销毁回调允许它参与到request scope的生命周期。这不是常见场景；内部bean通常只是分享在它们的包含bean的域内。
### 集合
在&lt;list/>，&lt;set/>，&lt;map/>和&lt;props />元素中，您可以分别设置Java Collection类型List，Set,Map和Properties的属性和参数。
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

*关于合并的这个部分讨论了父子bean的机制。读者不熟悉父和子bean定义的可能些昂在继续前阅读 [相关章节](Bean定义继承)*

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
当您设置bean属性时，可以使用复合或嵌套属性名称，只要除了最终属性名称之外的路径的所有组件都不为null。




