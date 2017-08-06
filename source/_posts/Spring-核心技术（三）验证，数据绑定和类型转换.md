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
<div style="border: 1px solid #ccc;background-color:#f8f8f8;padding:20px;">**JSR-303/JSR-349 Bean 验证**   
Spring Framework 4.0在安装支持方面支持Bean Validation 1.0（JSR-303）和Bean Validation 1.1（JSR-349），也适用于Spring的Validator接口。</br>

应用程序可以选择一次全局启用Bean验证，如[第8节“Spring验证”](#pring验证)中所述，并专门用于所有验证需求。

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

Bean包中的一个非常重要的类是BeanWrapper接口及其相应的实现（BeanWrapperImpl）。从javadocs引用，BeanWrapper提供了设置和获取属性值（单独或批量），获取属性描述符和查询属性以确定它们是可读写还是可写的功能。此外，BeanWrapper还提供对嵌套属性的支持，可以使子属性的属性设置为无限深度。然后，BeanWrapper支持添加标准JavaBeans PropertyChangeListeners和VetoableChangeListeners的能力，而不需要在目标类中有支持代码。最后但并非最不重要的是，BeanWrapper提供了对索引属性设置的支持。BeanWrapper通常不直接由应用程序代码使用，而是由DataBinder和BeanFactory使用。

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
Spring使用**PropertyEditors**的概念来实现Object和String之间的转换。如果你考虑到这一点,有时以一个不同的方式，而不是用对象本身来表示属性可能更为方便。例如，Date可以用人类可读的方式表示（以String '2007-14-09'），而我们仍然能够将人类可读的形式转换回原来的date（或甚至更好：将以人类可读形式输入的任何日期转换回Date对象）。

















