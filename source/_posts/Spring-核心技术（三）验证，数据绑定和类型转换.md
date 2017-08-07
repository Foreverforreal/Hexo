title: Spring 核心技术（三）验证，数据绑定和类型转换
id: 1501903516971
author: 不识
date: 2017-08-05 11:25:19
tags:
  - Spring
categories:
  - Spring
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

[原文链接](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#validation)
***
# 简介
***
<div style="border: 1px solid #ccc;background-color:#f8f8f8;padding:20px;">*JSR-303/JSR-349 Bean 验证*   
Spring Framework 4.0在安装支持方面支持Bean Validation 1.0（JSR-303）和Bean Validation 1.1（JSR-349），也适用于Spring的Validator接口。</br>
应用程序可以选择一次全局启用Bean验证，如[第8节“Spring验证”](#pring验证)中所述，并专门用于所有验证需求。</br>
应用程序还可以为每个DataBinder实例注册额外的Spring Validator实例，如[第8.3节“配置DataBinder”](#配置DataBinder)中所述。这可能有助于不使用注解来插入验证逻辑。
</div> 
将验证视为业务逻辑有利有弊，而Spring提供了一种验证（和数据绑定）的设计，不排除任何一种。明确的的验证不应该与Web层相关联，应该易于本地化，并且应该可以插入任何可用的验证器。考虑到上述情况，Spring已经提出了一个**Validator**接口，它应用程序的每一层都是基本和非常可用的。

数据绑定对于允许将用户输入动态绑定到应用程序的域模型（或用于处理用户输入的任何对象）是有用的。Spring提供了所谓的**DataBinder**来做到这一点。**Validator**和**DataBinder**构成**validation**包，主要用于但不限于MVC框架。

**BeanWrapper**是Spring框架中的一个基本概念，并且用于很多地方。但是，你可能不需要直接使用BeanWrapper。因为这是参考文档，我们觉得有些解释可能需要按顺序来。我们将在本章中解释BeanWrapper，如果你要使用它，你最可能在尝试绑定数据到对象的时候这么做。

Spring的DataBinder和较低级别的BeanWrapper都使用PropertyEditor来解析和格式化属性值。**PropertyEditor**概念是JavaBeans规范的一部分，本章还将对此进行说明。Spring 3引入了一个“core.convert”包，它提供一个通用的类型转换工具，而且还有一个更高级的“format”包,用于格式化UI字段值。这些新的包可能被用作PropertyEditor的更简单的替代方法，本章还将讨论这些新的软件包。

***
# 使用Spring Validator接口验证
***
Spring具有可用于验证对象的**Validator**接口。**Validator**接口使用Errors对象进行工作，以便在验证时，验证器可以向**Errors**对象报告验证失败。

我们来考虑一个小数据对象
```java
public class Person {

    private String name;
    private int age;

    // 通常的getters和setters...
}
```
我们将通过实现**org.springframework.validation.Validator**接口的以下两种方法为Person类提供验证行为：
- **supports(Class)** —— 此Validator可以验证提供的Class的实例吗？
- **validate(Object, org.springframework.validation.Errors)** —— 验证给定的对象，并在验证错误的情况下，注册这些错误给给定的**Errors**对象

实现Validator是非常简单的，特别是当您知道Spring Framework还提供的ValidationUtils帮助类时。
```java
public class PersonValidator implements Validator {

    /**
     * 这个Validator验证*只是*Person实例
     */
    public boolean supports(Class clazz) {
        return Person.class.equals(clazz);
    }

    public void validate(Object obj, Errors e) {
        ValidationUtils.rejectIfEmpty(e, "name", "name.empty");
        Person p = (Person) obj;
        if (p.getAge() < 0) {
            e.rejectValue("age", "negativevalue");
        } else if (p.getAge() > 110) {
            e.rejectValue("age", "too.darn.old");
        }
    }
}
```
如您所见，**ValidationUtils**类上的**static** **rejectIfEmpty(..)**方法用于拒绝'name'属性,如果它为null或是空字符串的话。看看ValidationUtils的javadocs，除了以前显示的例子，它提供了什么功能。

虽然确实可以通过实现一个单个Validator类来验证富对象中的每个嵌套对象，但最好为对象中的每个嵌套类在它自己的Validator实现中封装验证逻辑。一个'富'对象的简单示例是由两个String属性（第一个和第二个名称）以及一个复杂的Address对象组成的Customer。Address对象可能独立于Customer对象使用，因此一个不同的AddressValidator已经实现。如果你希望你的CustomerValidator无需复制和粘贴，就能重用AddressValidator类中包含的逻辑，你可以在你的CustomerValidator中依赖注入或实例化AddressValidator，如下一样使用：
```java
public class CustomerValidator implements Validator {

    private final Validator addressValidator;

    public CustomerValidator(Validator addressValidator) {
        if (addressValidator == null) {
            throw new IllegalArgumentException("The supplied [Validator] is " +
                "required and must not be null.");
        }
        if (!addressValidator.supports(Address.class)) {
            throw new IllegalArgumentException("The supplied [Validator] must " +
                "support the validation of [Address] instances.");
        }
        this.addressValidator = addressValidator;
    }

    /**
     * 此Validator验证Customer实例以及Customer的任何子类
     */
    public boolean supports(Class clazz) {
        return Customer.class.isAssignableFrom(clazz);
    }

    public void validate(Object target, Errors errors) {
        ValidationUtils.rejectIfEmptyOrWhitespace(errors, "firstName", "field.required");
        ValidationUtils.rejectIfEmptyOrWhitespace(errors, "surname", "field.required");
        Customer customer = (Customer) target;
        try {
            errors.pushNestedPath("address");
            ValidationUtils.invokeValidator(this.addressValidator, customer.getAddress(), errors);
        } finally {
            errors.popNestedPath();
        }
    }
}
```
验证错误被报告给传递给验证器的Errors对象。在Spring Web MVC的情况下，你可以使用&lt;spring:bind/>标签来检查错误消息，但是当然你也可以自己检查错误对象。有关其提供的方法的更多信息可以在javadoc中找到。

***
# 解析代码到错误消息
***
我们已经讨论了数据绑定和验证。输出与验证错误相对应的消息是我们需要讨论的最后一件事。在上面显示的示例中，我们拒绝了name和age字段。如果我们要使用**MessageSource**输出错误消息，我们将在拒绝该字段（在这种情况下为'name'和'age'）时使用已经给出的错误代码来完成输出。当你从Errors接口调用（直接，或间接，使用例如ValidationUtils类）**rejectValue**或者其他**reject**方法之一时，底层实现将不仅注册您传递的代码，还会注册一些其他错误代码。它要注册什么错误代码由所使用的**MessageCodesResolver**决定。默认情况下，使用**DefaultMessageCodesResolver**，例如，它不仅注册了您提供的代码的消息，还包括传递给reject方法的字段名称的消息。因此，如果你使用rejectValue("age", "too.darn.old")拒绝一个字段，除了too.darn.old代码之外，Spring还将注册too.darn.old.age和too.darn.old.age.int（所以第一个将包括字段名称，第二个将包括该字段的类型）;这样做是为了方便开发人员定位错误消息等。

有关MessageCodesResolver和默认策略的更多信息可以分别在MessageCodesResolver和DefaultMessageCodesResolver的javadoc中在线查找。

***
# Bean操作和BeanWrapper
***
**org.springframework.beans**包遵循Oracle提供的JavaBeans标准。JavaBean只是一个带有默认无参构造的类，它遵循一个命名规则（作为一个例子）一个名为bingoMadness的属性会有一个setter方法setBingoMadness(..)以及一个getter方法getBingoMadness()。有关JavaBeans和规范的更多信息，请参考Oracle网站（[javabeans](https://docs.oracle.com/javase/6/docs/api/java/beans/package-summary.html)）。

Bean包中的一个非常重要的类是**BeanWrapper**接口及其相应的实现（**BeanWrapperImpl**）。从javadocs引用，BeanWrapper提供了设置和获取属性值（单独或批量），获取属性描述符和查询属性以确定它们是可读写还是可写的功能。此外，**BeanWrapper**还提供对嵌套属性的支持，可以使子属性的属性设置为无限深度。然后，**BeanWrapper**支持添加标准JavaBeans **PropertyChangeListeners**和**VetoableChangeListeners**的能力，而不需要在目标类中有支持代码。最后但并非最不重要的是，BeanWrapper提供了对索引属性设置的支持。BeanWrapper通常不直接由应用程序代码使用，而是由**DataBinder**和**BeanFactory**使用。

BeanWrapper的工作方式部分由其名称指示：它包装一个bean以对该bean执行操作，例如设置和检索属性。

## 设置和获取基本及嵌套属性
***
属性的设置和获取是使用setPropertyValue(s)和getPropertyValue(s)方法完成的，这两个方法都带有几个重载变体。它们都在Spring附带的javadocs中有更详细的描述。重要的是知道这有几个约定用于指示对象的属性。几个例子：

*属性示例*

|表达式|说明|
|------|----|
|**name**|指示与方法getName()或isName()和setName(..)对应的属性**name**|
|**account.name**|指示与方法getAccount().setName()或getAccount().getName()对应的属性account的嵌套属性name。|
|**account[2]**|指示索引属性**account**的第三个元素。索引属性可以是*array，list或其他自然排序集合类型。|
|**account[COMPANYNAME]**|指示由Map属性**account**的键COMPANYNAME索引的map entry的值|

下面你会发现一些使用BeanWrapper来获取和设置属性的例子。

*（如果你不打算直接使用BeanWrapper，下一节对您来说并不重要。如果你只是使用DataBinder和BeanFactory及其开箱即用的实现，您应该跳过关于PropertyEditors的部分。）*

考虑以下两个类：
```java
public class Company {

    private String name;
    private Employee managingDirector;

    public String getName() {
        return this.name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Employee getManagingDirector() {
        return this.managingDirector;
    }

    public void setManagingDirector(Employee managingDirector) {
        this.managingDirector = managingDirector;
    }
}
```
```java
public class Employee {

    private String name;

    private float salary;

    public String getName() {
        return this.name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public float getSalary() {
        return salary;
    }

    public void setSalary(float salary) {
        this.salary = salary;
    }
}
```
以下代码片段显示了如何检索和操作实例化的Companies和Employees的某些属性的示例：
```java
BeanWrapper company = new BeanWrapperImpl(new Company());
// 设置company name..
company.setPropertyValue("name", "Some Company Inc.");
// ...也可以这样做:
PropertyValue value = new PropertyValue("name", "Some Company Inc.");
company.setPropertyValue(value);

// 好的，让我们创建一个director并把它绑在company上：:
BeanWrapper jim = new BeanWrapperImpl(new Employee());
jim.setPropertyValue("name", "Jim Stravinsky");
company.setPropertyValue("managingDirector", jim.getWrappedInstance());

// 通过company检索manageDirector的salary 
Float salary = (Float) company.getPropertyValue("managingDirector.salary");
```
## 内置PropertyEditor实现
***
Spring使用**PropertyEditor**的概念来实现Object和String之间的转换。如果你考虑到这一点,有时以一个不同的方式，而不是用对象本身来表示属性可能更为方便。例如，Date可以用人类可读的方式表示（以String '2007-14-09'），而我们仍然能够将人类可读的形式转换回原来的date（或甚至更好：将以人类可读形式输入的任何日期转换回Date对象）。这种行为可以通过注册**java.beans.PropertyEditor**类型的自定义编辑器来实现。在BeanWrapper或者在上一章中提到的特定IoC容器中注册自定义编辑器，可以了解如何将属性转换为所需类型。在Oracle提供的java.beans包的javadoc中阅读有关PropertyEditors的更多信息。

Spring中使用了一些属性编辑的例子：
- 在bean上设置属性是通过使用PropertyEditors完成的。当提及的java.lang.String作为您声明在XML文件中的某个bean的属性的值时，Spring会（如果相应属性的setter具有Class参数）使用ClassEditor尝试将参数解析为一个Class对象。
- 在Spring的MVC框架中解析HTTP请求参数是使用各种PropertyEditor完成的，这些PropertyEditors可以手动绑定CommandController的所有子类。

Spring有一些内置的PropertyEditor，使工作变得轻松。其中的每个都列在下面，它们都位于org.springframework.beans.propertyeditors包中。大多数但不是全部（如下所示），默认情况下由BeanWrapperImpl注册。属性编辑器可以以一些方式配置，你当然可以注册自己的变体来覆盖默认的：
```
Built-in PropertyEditors

|Class|说明|
|----|----|
|**ByteArrayPropertyEditor**|字节数组编辑器。String将简单的转换为它们的字节表现形式。由BeanWrapperImpl默认注册|
|ClassEditor|解析String表示的class为实际的类和反过来也是。当没有找到这个类时，抛出IllegalArgumentException。由BeanWrapperImpl默认注册 |
|**CustomBooleanEditor**|Boolean属性的可自定义属性编辑器。由BeanWrapperImpl默认注册，但是，可以通过将其自定义实例注册为自定义编辑器来覆盖。|
|**CustomCollectionEditor|Collection的属性编辑器，将任何源Collection转换为给定的目标Collection类型。|
|**CustomDateEditor**|可自定义的java.util.Date属性编辑器，支持自定义DateFormat。默认情况下未注册。必须使用适当format进行用户注册。|
|**CustomNumberEditor**|任何Number子类的可自定义属性编辑器，如Integer，Long，Float，Double。由BeanWrapperImpl默认注册，但是可以通过将其自定义实例注册为自定义编辑器来覆盖。|
|**FileEditor**|能够将String解析为java.io.File对象。由BeanWrapperImpl默认注册。|
|**InputStreamEditor**|单向属性编辑器，能够获取文本字符串并生成（通过中间的ResourceEditor和Resource）InputStream，因此InputStream属性可以直接设置为String。请注意，默认使用情况不会为你关闭InputStream！由BeanWrapperImpl默认注册。|
|**LocaleEditor**|能够将String解析为Locale对象，反之亦然（String格式为[country] [variant]，这与Locale提供的toString()方法相同）。由BeanWrapperImpl默认注册。|
|**PatternEditor**|能够将String解析为java.util.regex.Pattern对象，反之亦然。|
|**PropertiesEditor**|能够转换String（使用java.util.Properties类的javadocs中定义的格式格式化）为Properties对象。由BeanWrapperImpl默认注册。|
|**StringTrimmerEditor**|修剪字符串的属性编辑器。可选的允许将空字符串转换为null值。默认情况下未注册;必须根据需要进行用户注册。|
|**URLEditor**|能够将URL的String表示形式解析为实际的URL对象。由BeanWrapperImpl默认注册。|

Spring使用java.beans.PropertyEditorManager设置可能需要的属性编辑器的搜索路径。搜索路径中还包括sun.bean.editors，它包括用于诸如Font，Color和大多数原始类型之类的PropertyEditor实现。还要注意的是，标准的JavaBeans基础框架会自动发现PropertyEditor类（不需要显示地注册它们），如果它们与他们处理的类在同一个包中，并且具有与该类相同的附加“Editor”的名称;例如，可能有如下类和包结构，这足以使FooEditor类被识别并用作Foo类型属性的PropertyEditor。
```
com
  chank
    pop
      Foo
      FooEditor // Foo类的PropertyEditor
```
请注意，你也可以在这里使用标准的BeanInfo JavaBeans机制（[在这里不详细](https://docs.oracle.com/javase/tutorial/javabeans/advanced/customization.html)的描述）。在下面的一个示例中，使用BeanInfo机制来显式注册一个或多个具有关联类的属性的PropertyEditor实例。
```
com
  chank
    pop
      Foo
      FooBeanInfo // Foo类的BeanInfo 
```
这里是被引用的FooBeanInfo类的Java源代码。这会将一个CustomNumberEditor与Foo类的age属性关联起来。
```java
public class FooBeanInfo extends SimpleBeanInfo {

    public PropertyDescriptor[] getPropertyDescriptors() {
        try {
            final PropertyEditor numberPE = new CustomNumberEditor(Integer.class, true);
            PropertyDescriptor ageDescriptor = new PropertyDescriptor("age", Foo.class) {
                public PropertyEditor createPropertyEditor(Object bean) {
                    return numberPE;
                };
            };
            return new PropertyDescriptor[] { ageDescriptor };
        }
        catch (IntrospectionException ex) {
            throw new Error(ex.toString());
        }
    }
}
```
### 注册其他自定义PropertyEditor
当将bean属性设置为字符串值时，Spring IoC容器最终会使用标准的JavaBeans PropertyEditors将这些字符串转换为该属性的复杂类型。Spring预注册了一些自定义的PropertyEditor（例如，将一个字符串表达的类名转换为一个真正的Class对象）。另外，Java的标准JavaBeans PropertyEditor查找机制允许一个类的PropertyEdito只需被适当地命名，并且被放置在与它提供的类相同的包中，就会被自动找到。

如果需要注册其他自定义PropertyEditor，这有几种可用机制。最手动的方式，也是通常不方便或不推荐的，是只要使用ConfigurableBeanFactory接口的registerCustomEditor()方法，假设你有一个BeanFactory引用的话。另一个稍微更方便的机制就是使用一个名为CustomEditorConfigurer的特殊bean工厂的后处理器。虽然Bean工厂后处理器可以与BeanFactory实现一起使用，但CustomEditorConfigurer有一个嵌套属性设置，因此强烈建议将其与ApplicationContext一起使用，它可以与其他任何bean类似的样式被部署，并且被自动检测和应用。

请注意，所有bean工厂和应用程序上下文都会自动使用一些内置的属性编辑器，通过使用一些名为**BeanWrapper**的东西来处理属性转换。**BeanWrapper**注册的标准属性编辑器在上一节中都被列出。此外，**ApplicationContexts**还会以适合特定应用程序上下文类型的方式覆盖或添加额外数量的编辑器来处理资源查找。

标准JavaBeans **PropertyEditor**实例用于将表示为字符串的属性值转换为属性的实际复杂类型。**CustomEditorConfigurer**，一个bean工厂后处理器，可以用来方便地向ApplicationContext添加额外的PropertyEditor实例。

考虑一个用户类ExoticType，另一个类DependsOnExoticType需要ExoticType设置为一个属性：
```java
package example;

public class ExoticType {

    private String name;

    public ExoticType(String name) {
        this.name = name;
    }
}

public class DependsOnExoticType {

    private ExoticType type;

    public void setType(ExoticType type) {
        this.type = type;
    }
}
```
当这些正确设置后，我们希望能够将type属性以字符串形式分配，PropertyEditor会在后台将其转换为实际的ExoticType实例：
```xml
<bean id="sample" class="example.DependsOnExoticType">
    <property name="type" value="aNameForExoticType"/>
</bean>
```
**PropertyEditor**实现可能类似于：
```java
// 将字符串表示转换为ExoticType对象 
package example;

public class ExoticTypeEditor extends PropertyEditorSupport {

    public void setAsText(String text) {
        setValue(new ExoticType(text.toUpperCase()));
    }
}
```
最后，我们使用**CustomEditorConfigurer**向**ApplicationContext**注册新的**PropertyEditor**，然后可以根据需要使用它：
```xml
<bean class="org.springframework.beans.factory.config.CustomEditorConfigurer">
    <property name="customEditors">
        <map>
            <entry key="example.ExoticType" value="example.ExoticTypeEditor"/>
        </map>
    </property>
</bean>
```
### 使用PropertyEditorRegistrars
使用Spring容器注册属性编辑器的另一种机制是创建并使用**PropertyEditorRegistrar**。当你需要在几种不同情况下使用同一组属性编辑器时，此接口特别有用：编写一个相应的registrar并且再每种情况下重用它。**PropertyEditorRegistrars**与名为**PropertyEditorRegistry**的接口配合使用，PropertyEditorRegistry是一个由Spring BeanWrapper（和DataBinder）实现的接口。当PropertyEditorRegistrars与**CustomEditorConfigurer**（[这里](#注册其他自定义PropertyEditor)介绍）一起使用时特别方便，它暴露出一个名为setPropertyEditorRegistrars(..)的属性：以这种方式添加到CustomEditorConfigurer中的PropertyEditorRegistrars可以轻松地与DataBinder和Spring MVC Controllers共享。此外，它避免了在自定义编辑器上同步的需要：PropertyEditorRegistrar预期为每个bean的创建企图创建新的PropertyEditor实例。

使用PropertyEditorRegistrar或许最好通过一个例子来说明。首先，你需要创建自己的PropertyEditorRegistrar实现：
```java
package com.foo.editors.spring;

public final class CustomPropertyEditorRegistrar implements PropertyEditorRegistrar {

    public void registerCustomEditors(PropertyEditorRegistry registry) {

        // 希望将创建新的PropertyEditor实例
        registry.registerCustomEditor(ExoticType.class, new ExoticTypeEditor());

        // 你可以在这里注册尽可能多的自定义属性编辑器...
    }
}
```
还可以参阅**org.springframework.beans.support.ResourceEditorRegistrar**作为**PropertyEditorRegistrar**实现的一个示例。注意在它的registerCustomEditors(..)方法的实现中如何创建每个属性编辑器的新实例。

接下来，我们配置一个**CustomEditorConfigurer**并将我们的**CustomPropertyEditorRegistrar**的一个实例注入它：
```xml
<bean class="org.springframework.beans.factory.config.CustomEditorConfigurer">
    <property name="propertyEditorRegistrars">
        <list>
            <ref bean="customPropertyEditorRegistrar"/>
        </list>
    </property>
</bean>

<bean id="customPropertyEditorRegistrar"
    class="com.foo.editors.spring.CustomPropertyEditorRegistrar"/>
```
最后，与本章重点有所偏离，对于那些使用Spring MVC Web框架的人，使用与数据绑定的**Controllers**（如**SimpleFormController**）结合使用的**PropertyEditorRegistrars**非常方便。在下面的一个示例中，使用PropertyEditorRegistrar实现一个initBinder(..)方法：
```java
public final class RegisterUserController extends SimpleFormController {

    private final PropertyEditorRegistrar customPropertyEditorRegistrar;

    public RegisterUserController(PropertyEditorRegistrar propertyEditorRegistrar) {
        this.customPropertyEditorRegistrar = propertyEditorRegistrar;
    }

    protected void initBinder(HttpServletRequest request,
            ServletRequestDataBinder binder) throws Exception {
        this.customPropertyEditorRegistrar.registerCustomEditors(binder);
    }

    // 注册用户的其他方法
}
```
这种风格的PropertyEditor注册可以导致简洁的代码（initBinder(..)的实现只有一行！），并允许通用的PropertyEditor注册代码封装在一个类中，然后根据需要在多个Controller之间共享。

***
# Spring类型转换
***
Spring 3引入了一个core.convert包来提供通用的类型转换系统。该系统定义了一个SPI来实现类型转换逻辑，以及一个在运行时执行类型转换的API。在Spring容器中，该系统可以用作PropertyEditor的替代方法，将外部化的bean属性值的字符串转换为必需的属性类型。这个公共API也可以在应用程序中需要类型转换的任何地方使用。

## Converter SPI
***
SPI实现类型转换逻辑是简单和强类型的：
```java
package org.springframework.core.convert.converter;

public interface Converter<S, T> {

    T convert(S source);

}
```
要创建自己的转换器，只需实现上面的接口。参数化S作为你需要转换的类型，而T是你要转换为的类型。

对于convert(S)的每个调用，源参数保证不为null。如果转换失败，转换器可能会抛出任何未经检查的异常;具体来说，应该抛出一个IllegalArgumentException来报告一个无效的源值。注意确保你的Converter实现是线程安全的。

为方便起见，在core.convert.support包中提供了几个转换器实现。这些包括从Strings到Numbers和其他常见类型的转换器。以StringToInteger为例，介绍典型的Converter实现：

```java
package org.springframework.core.convert.converter;

public interface Converter<S, T> {

    T convert(S source);

}
```
## ConverterFactory
***
当您需要集中整个类层次结构的转换逻辑时，例如，当从String转换为java.lang.Enum对象时，实现ConverterFactory：
```java
package org.springframework.core.convert.converter;

public interface ConverterFactory<S, R> {

    <T extends R> Converter<S, T> getConverter(Class<T> targetType);

}
```
参数化S是你需要转换的类型，R为定义您转换为类的范围的基本类型。然后实现getConverter(Class &lt;T>)，其中T是R的子类。然后实现getConverter（Class <T>），其中T是R的子类。

以StringToEnum ConverterFactory为例：
```java
package org.springframework.core.convert.support;

final class StringToEnumConverterFactory implements ConverterFactory<String, Enum> {

    public <T extends Enum> Converter<String, T> getConverter(Class<T> targetType) {
        return new StringToEnumConverter(targetType);
    }

    private final class StringToEnumConverter<T extends Enum> implements Converter<String, T> {

        private Class<T> enumType;

        public StringToEnumConverter(Class<T> enumType) {
            this.enumType = enumType;
        }

        public T convert(String source) {
            return (T) Enum.valueOf(this.enumType, source.trim());
        }
    }
}
```
## GenericConverter
***
当你需要一个复杂的Converter实现时，请考虑GenericConverter接口。GenericConverter具有更灵活但不太强的类型签名，支持在多种源和目标类型之间进行转换。此外，GenericConverter可以提供源和目标字段的上下文，这样你可以在实现转换逻辑时使用。这种上下文允许类型转换由字段注解或在字段签名上声明的泛型信息来驱动。
```java
package org.springframework.core.convert.converter;

public interface GenericConverter {

    public Set<ConvertiblePair> getConvertibleTypes();

    Object convert(Object source, TypeDescriptor sourceType, TypeDescriptor targetType);

}
```
要实现GenericConverter，getConvertibleTypes()返回支持的源→目标类型对。然后实现convert(Object，TypeDescriptor，TypeDescriptor)来实现你的转换逻辑。转换源的TypeDescriptor提供对持有正在转换的值的转换源字段的访问。转换目标的TypeDescriptor提供对会被设置转换后值的目标字段的访问。

GenericConverter是转换器的一个很好的例子是在Java Array和Collection之间进行转换。这样的ArrayToCollectionConverter会内省声明目标Collection类型的字段，来解析Collection的元素类型。




