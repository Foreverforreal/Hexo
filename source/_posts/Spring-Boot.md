title: Spring Boot
id: 1525484943140
author: 不识
tags:
  - spring
  - spring boot
categories:
  - Spring Boot
date: 2018-05-05 09:49:00
---
[原文链接](https://docs.spring.io/spring-boot/docs/2.0.2.RELEASE/reference/htmlsingle/)

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

***
# Spring Boot入门
***
如果你想大体入门Spring Boot或Spring，请从阅读本章节开始。它回答了一些基本的“这是什么”，“如何来用”以及为什么的问题。它包括Spring Boot的介绍以及安装说明。然后，我们将引导您构建您的第一个Spring Boot应用程序，并讨论一些核心原理。
## Spring Boot介绍
***
Spring Boot可以轻松创建你可以运行的独立的，生产级的基于Spring的应用程序。我们有对Spring平台和第三方库有自己的看法，以便你花费尽可能少的麻烦来学习。大多数SpringBoot应用程序只需要极少的Spring配置。
<!-- more -->
您可以使用Spring Boot来创建可以使用**java -jar**或更传统的wai部署启动的Java应用程序。我们还提供了一个命令行工具可以运行“spring脚本”。
我们的主要目标是：
- 为所有Spring开发提供一个更快，更广泛的入门体验。
- 自认为可以开箱即用，但是尽可能快的随着需求脱离默认设置开始自定义配置
- 提供一系列大型项目常见的非功能性功能（如嵌入式服务器，安全性，指标，运行状况检查和外部配置）。
- 绝对不会生成代码，并且不需要XML配置。

## 系统要求
***
Spring Boot 2.0.1.RELEASE需要Java 8或9以及Spring Framework 5.0.5.RELEASE或更高版本。为Maven 3.2+和Gradle 4提供了明确的构建支持。

### servlet容器
Spring Boot支持以下嵌入式servlet容器：

|名称|servlet版本|
|---|---|
|Tomcat 8.5|3.1|
|Jetty 9.4 |3.1|
|Undertow 1.4|3.1|
您也可以将Spring Boot应用程序部署到任何与Servlet 3.1+兼容的容器。

## 安装Spring Boot
***
Spring Boot可以与“经典”Java开发工具一起使用，也可以作为命令行工具安装。无论哪种方式，您都需要Java SDK v1.8或更高版本。在开始之前，您应该使用以下命令检查当前的Java安装：
```bash
$ java -version
```
如果您对Java开发不熟悉，或者想要试验Spring Boot，则可能需要先尝试Spring Boot CLI（命令行界面）。否则，请阅读“经典”安装说明。

### Java开发人员的安装说明
您可以像使用任何标准Java库一样使用Spring Boot。为此，请在类路径中包含相应的**spring-boot - \*.jar**文件。Spring Boot不需要任何特殊的工具集成，因此您可以使用任何IDE或文本编辑器。此外，Spring Boot应用程序没有什么特别之处，因此您可以像运行其他任何Java程序一样运行和调试Spring Boot应用程序。

虽然您可以复制Spring Boot jar，但我们通常建议您使用支持依赖管理的构建工具（如Maven或Gradle）。

#### Maven安装
Spring Boot与Apache Maven 3.2或更高版本兼容。如果您尚未安装Maven，则可以按照[maven.apache.org](https://maven.apache.org/)上的说明进行操作。

> 在大多操作系统上，Maven可以使用包管理器安装。如果您使用OSX Homebrew，请尝试**brew install maven**。 Ubuntu用户可以运行**sudo apt-get install maven**。具有Chocolatey的Windows用户可以从提升（管理员）提示符下运行**choco install maven**。

Spring Boot依赖使用**org.springframework.boot**这个**groupId**，通常，您的Maven POM文件继承自**spring-boot-starter-parent**项目并将依赖关系声明为一个或多个“Starter”。Spring Boot还提供了一个可选的Maven插件来创建可执行的jar。
以下列表显示了一个典型的pom.xml文件：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>myproject</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<!-- 继承Spring Boot的默认值 -->
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.1.RELEASE</version>
	</parent>

	<!-- 为Web应用程序添加典型的依赖 -->
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
	</dependencies>

	<!-- 打包成可执行的jar文件 -->
	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>
```

> **Spring-Boot-starter-parent**是使用Spring Boot的好方法，但它可能并不是所有的时候都合适。有时您可能需要继承另一个不同的父POM，或者您可能不喜欢我们的默认设置。在这些情况下，请参见第13.2.2节“使用没有父POM的Spring Boot”，了解使用导入范围的替代解决方案。

#### Gradle安装
Spring Boot与Gradle 4兼容。如果您还没有安装Gradle，可以按照[gradle.org](https://gradle.org/)上的说明进行操作。
Spring Boot依赖可以通过使用**org.springframework.boot**group来声明。通常，您的项目将依赖项声明为一个或多个“Starter”。 Spring Boot提供了一个有用的Gradle插件，可以用来简化依赖声明和创建可执行的jar。

> Gradle Wrapper
当您需要构建项目时，Gradle Wrapper提供了一种“获取”Gradle的好方法。它是一个小脚本和库，与代码一起提交以引导构建过程。有关详细信息，请参阅docs.gradle.org/4.2.1/userguide/gradle\_wrapper.html。

以下示例显示了一个典型的**build.gradle**文件：
```
plugins {
	id 'org.springframework.boot' version '2.0.1.RELEASE'
	id 'java'
}


jar {
	baseName = 'myproject'
	version =  '0.0.1-SNAPSHOT'
}

repositories {
	jcenter()
}

dependencies {
	compile("org.springframework.boot:spring-boot-starter-web")
	testCompile("org.springframework.boot:spring-boot-starter-test")
}
```

### 安装Spring Boot CLI
省略

### 从较早版本的Spring Boot升级
如果您是从早期版本的Spring Boot进行升级，请查看项目wiki上的“[迁移指南](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide)”，其中提供了详细的升级说明。还要检查“[发行说明](https://github.com/spring-projects/spring-boot/wiki)”，以获取每个发行版的“新功能”和“值得注意”功能列表。
要升级现有的CLI安装，请使用相应的软件包管理器命令（例如，brew升级），或者如果您手动安装了CLI，请遵循标准说明，记住更新PATH环境变量以删除所有旧的引用。


## 开发你的第一个Spring Boot应用程序
***
本节介绍如何开发一个简单的“Hello World！”Web应用程序，该应用程序重点介绍Spring Boot的一些主要功能。我们使用Maven来构建这个项目，因为大多数IDE都支持它。
> [spring.io](https://spring.io/)网站包含许多使用Spring Boot的“[入门指南](https://spring.io/guides)”。如果您需要解决特定问题，请先在那里查看。
您可以通过转到[start.spring.io](https://start.spring.io/)并从依赖关系搜索器中选择“Web”starter来缩短以下步骤。这样做会产生一个新的项目结构，以便您可以立即开始编码。查看[Spring Initializr文档](https://github.com/spring-io/initializr)以获取更多详细信息。

在开始之前，请打开终端并运行以下命令以确保您已安装了Java和Maven的有效版本：
```bash
$ java -version
java version "1.8.0_102"
Java(TM) SE Runtime Environment (build 1.8.0_102-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.102-b14, mixed mode)
```
```bash
$ mvn -v
Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-10T16:41:47+00:00)
Maven home: /usr/local/Cellar/maven/3.3.9/libexec
Java version: 1.8.0_102, vendor: Oracle Corporation
```
>此示例需要在其自己的文件夹中创建。后续说明假定您已经创建了合适的文件夹，并且它是您当前的目录。

### 创建POM
我们需要从创建一个Maven **pom.xml**文件开始。 **pom.xml**是用于构建项目的配方。打开您最喜欢的文本编辑器并添加以下内容：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>myproject</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.1.RELEASE</version>
	</parent>

	<!-- 在这里添加额外的行... -->

</project>
```
前面的列表应该给你一个可运行的构建。你可以通过运行**mvn package**来测试它（现在，你可以忽略“jar will be empty - no content was marked for inclusion！”警告）。
> 此时，您可以将项目导入IDE（大多数现代Java IDE包含对Maven的内置支持）。为了简单起见，我们在本例中继续使用纯文本编辑器。

### 添加类路径依赖
Spring Boot提供了一系列“Starter”，可让您将jar添加到类路径中。我们的示例应用程序已经在POM的父节点中使用了**spring-boot-starter-parent**。 **spring-boot-starter-parent**是一个特别的starter，它提供了有用的Maven默认值。它还提供了一个 [dependency-management](https://docs.spring.io/spring-boot/docs/2.0.1.RELEASE/reference/htmlsingle/#using-boot-dependency-management)部分，以便您可以在其他依赖中省略**version**标签。
其他“Starter”提供了在开发特定类型的应用程序时可能需要的依赖。
由于我们正在开发一个Web应用程序，因此我们添加了**spring-boot-starter-web**依赖项。在此之前，我们可以通过运行以下命令来查看我们目前的功能：
```bash
$ mvn dependency:tree

[INFO] com.example:myproject:jar:0.0.1-SNAPSHOT
```
**mvn dependency:tree**命令打印项目依赖关系的树形表示。你可以看到**spring-boot-starter-parent**本身不提供任何依赖关系。要添加必要的依赖项，请编辑您的**pom.xml**并在**parent **下方添加**spring-boot-starter-web**依赖项：
```xml
<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-web</artifactId>
	</dependency>
</dependencies>
```
如果您再次运行**mvn dependency:tree**，则会发现现在有许多其他依赖项，包括Tomcat Web服务器和Spring Boot本身。

### 编写代码
为了完成我们的应用程序，我们需要创建一个Java文件。默认情况下，Maven会从**src / main / java**编译源代码，因此您需要创建该文件夹结构，然后添加名为**src / main / java / Example.java**的文件以包含以下代码：
```java
import org.springframework.boot.*;
import org.springframework.boot.autoconfigure.*;
import org.springframework.web.bind.annotation.*;

@RestController
@EnableAutoConfiguration
public class Example {

	@RequestMapping("/")
	String home() {
		return "Hello World!";
	}

	public static void main(String[] args) throws Exception {
		SpringApplication.run(Example.class, args);
	}

}
```
虽然这里没有太多的代码，但后面还是有很多。我们将在接下来的几节中介绍一些重要的部分。

#### @RestController和@RequestMapping注解
我们的**Example**类的第一个注解是**@RestController**。这被称为stereotype注解。它为阅读代码的人提供了线索，对于Spring来说，这个类扮演着特定的角色。在这种情况下，我们的类是一个web **@Controller**，所以Spring在处理传入的Web请求时会考虑它。
**@RequestMapping**注解提供了“路由”信息。它告诉Spring，任何带有**/** 路径的HTTP请求都应该映射到**home**方法。 **@RestController**注解告诉Spring将结果字符串直接呈现给调用者。

> **@RestController**和**@RequestMapping**注解是Spring MVC注解。 （它们并不特定于Spring Boot。）有关更多详细信息，请参阅Spring参考手册中的{% post_link Spring Web（一）MVC框架 %}部分。

#### @EnableAutoConfiguration注解
第二个类级注解是**@EnableAutoConfiguration**。这个注解告诉Spring Boot根据你添加的jar依赖来“猜测”你想要如何配置Spring。由于**spring-boot-starter-web**添加了Tomcat和Spring MVC，因此自动配置假定您正在开发Web应用程序并相应地设置Spring。
> Starters与Auto-Configuration
Auto-Configuration旨在与“Starter”配合使用，但这两个概念并不直接相关。您可以自由选择初学者之外的jar依赖项。 Spring Boot仍然尽力自动配置您的应用程序。

#### “main”方法
我们应用程序的最后一部分是**main**方法。这只是一个遵循Java约定的应用程序入口点的标准方法。我们的**main**方法通过调用**run**来委托Spring Boot的**SpringApplication**类。 **SpringApplication**启动我们的应用程序，从Spring开始，然后启动自动配置的Tomcat Web服务器。我们需要将**Example.class**作为参数传递给**run**方法，以告知**SpringApplication**哪一个是Spring的主要组件。 **args**数组也被传递以暴露任何命令行参数。

### 运行示例
此时你的应用程序应该可以工作了，由于你使用**spring-boot-starter-parent**，你有一个可用的**run** goal，你可以用它来启动应用程序。从项目根目录键入**mvn spring-boot：run**以启动应用程序。您应该看到类似于以下内容的输出：
```bash
$ mvn spring-boot:run

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::  (v2.0.1.RELEASE)
....... . . .
....... . . . (log output here)
....... . . .
........ Started Example in 2.222 seconds (JVM running for 6.514)
```
如果您打开一个Web浏览器到localhost：8080，您应该看到以下输出：
```
Hello World!
```
要正常退出应用程序，请按ctrl-c。

### 创建一个可执行的Jar
我们通过创建一个完全独立的可执行jar文件来完成我们的示例，该文件可以在生产环境中运行。可执行jar（有时称为“fat jars”）是包含您的编译类以及您的代码需要运行的所有jar依赖项的归档文件。
<div style="border: 1px solid #ccc;background-color:#f8f8f8;padding:20px;">
**可执行的jar和Java**</br>
Java没有提供加载嵌套jar文件的标准方式（本身包含在jar中的jar文件）。如果您想分发自包含的应用程序，这可能会有问题。</br>
为了解决这个问题，许多开发者使用“uber”jar。一个超级jar将所有应用程序依赖关系中的所有类打包到一个单独的存档中。这种方法的问题是很难看到你的应用程序中有哪些库。如果在多个jar中使用相同的文件名（但是具有不同的内容），则它也可能是有问题的。Spring Boot采用了[不同的方法](https://docs.spring.io/spring-boot/docs/2.0.1.RELEASE/reference/htmlsingle/#executable-jar)，可以让您直接嵌入jar。
</div>

要创建一个可执行的jar，我们需要将**spring-boot-maven-plugin**添加到我们的**pom.xml**中。为此，请在**dependencies**的下面插入以下几行：
```xml
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
	</plugins>
</build>
```
> **spring-boot-starter-parent** POM包含**&lt; executions&gt; **配置来绑定重新打包目标。如果您不使用父POM，则需要自行声明此配置。有关详细信息，请参阅插件文档。

保存你的**pom.xml**并从命令行运行**mvn package**，如下所示：
```xml
$ mvn package

[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building myproject 0.0.1-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] .... ..
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ myproject ---
[INFO] Building jar: /Users/developer/example/spring-boot-example/target/myproject-0.0.1-SNAPSHOT.jar
[INFO]
[INFO] --- spring-boot-maven-plugin:2.0.1.RELEASE:repackage (default) @ myproject ---
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
```
如果您查看**target**目录，则应该看到**myproject-0.0.1-SNAPSHOT.jar**。该文件的大小应该在10 MB左右。如果你想在里面偷看，你可以使用**jar tvf**，如下所示：
```bash
$ jar tvf target/myproject-0.0.1-SNAPSHOT.jar
```
您还应该在**target**目录中看到名为**myproject-0.0.1-SNAPSHOT.jar.original**的小得多的文件。这是Maven在被Spring Boot重新打包之前创建的原始jar文件。
要运行该应用程序，请使用**java -jar**命令，如下所示：
```bash
$ java -jar target/myproject-0.0.1-SNAPSHOT.jar

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::  (v2.0.1.RELEASE)
....... . . .
....... . . . (log output here)
....... . . .
........ Started Example in 2.536 seconds (JVM running for 2.864)
```
和以前一样，要退出应用程序，请按ctrl-c。
## 接下来要阅读的内容
***
希望本节提供了一些Spring Boot的基础知识，并帮助您编写自己的应用程序。如果您是面向任务的开发人员，则可能需要跳至spring.io，查看一些入门指南，以解决具体的“我该如何使用Spring？”问题。我们也有Spring Boot专用的“How-to”参考文档。
Spring Boot存储库还有一堆你可以运行的样本。样本与代码的其余部分无关（也就是说，您不需要构建其余代码以运行或使用样本）。
否则，下一个逻辑步骤是阅读第三部分“使用Spring Boot”。如果你真的不耐烦，你也可以直接跳过去阅读Spring Boot的功能。

***
# 使用Spring Boot
***
强烈建议您选择一个支持依赖关系管理的构建系统，并且可以使用发布到“Maven Central”存储库的artifacts 。我们建议您选择Maven或Gradle。 Spring Boot可以与其他构建系统（例如Ant）一起工作，但它们并没有得到特别好的支持。
## 构建系统
***

### Maven

#### 继承Starter父项
#### 使用没有父POM的Spring Boot
#### 使用Spring Boot Maven插件

### Gradle
### Ant
### Starters
Starters是一套方便的依赖描述符，你可以将其包含在应用程序中。您可以获得所需的所有Spring及相关技术的一站式商店，而无需查看示例代码并复制粘贴依赖描述符。例如，如果您想开始使用Spring和JPA进行数据库访问，请在项目中包含**spring-boot-starter-data-jpa**依赖项。
Starters包含很多依赖项，您需要快速启动并快速运行项目，并且需要一组支持的可传递依赖关系。
<div style="border: 1px solid #ccc;background-color:#f8f8f8;padding:20px;">**名称是怎么的**</br>所有官方的starters都遵循一个相似的明明模式**spring-boot-starter-\***,其中\*是特定类型的应用程序。许多IDE中的Maven集成允许您按名称搜索依赖项。例如，通过安装适当的Eclipse或STS插件，您可以在POM编辑器中按**ctrl-space**并键入“spring-boot-starter”获取完整列表。</br>
正如“[创建自己的启动器](https://docs.spring.io/spring-boot/docs/2.0.1.RELEASE/reference/htmlsingle/#boot-features-custom-starter)”部分所述，第三方启动器不应该从**spring-boot**开始，因为它是为官方Spring Boot工件保留的。相反，第三方starter通常以项目名称开头。例如，名为**thirdpartyproject**的第三方启动器项目通常会被命名为**thirdpartyproject-spring-boot-starter**。
</div>

以下starter应用程序由Spring Boot在org.springframework.boot组下提供：

|名称|描述|Pom|
|---|----|-----|
|**spring-boot-starter**| 核心入门者，包括自动配置支持，日志记录和YAML| |
|**spring-boot-starter-activemq**| | |
|**spring-boot-starter-amqp**| | |
|**spring-boot-starter-aop**| | |
|**spring-boot-starter-artemis**| | |
|**spring-boot-starter-batch**| | |
|**spring-boot-starter-cache**| | |
|**spring-boot-starter-cloud-connectors**| | |
|**spring-boot-starter-data-cassandra**| | |
|**spring-boot-starter-data-cassandra-reactive**| | |
|**spring-boot-starter-data-couchbase**| | |
|**spring-boot-starter-data-couchbase-reactive**| | |
|**spring-boot-starter-data-elasticsearch**| | |
|**spring-boot-starter-data-jpa**| | |
|**spring-boot-starter-data-ldap**| | |
|**spring-boot-starter-data-mongodb**| | |
|**spring-boot-starter-data-mongodb-reactive**| | |
|**spring-boot-starter-data-neo4j**| | |
|**spring-boot-starter-data-redis**| | |
|**spring-boot-starter-data-redis-reactive**| | |
|**spring-boot-starter-data-rest**| | |
|**spring-boot-starter-data-solr**| | |
|**spring-boot-starter-freemarker**| | |
|**spring-boot-starter-groovy-templates**| | |
|**spring-boot-starter-hateoas**| | |
|**spring-boot-starter-integration**| | |
|**spring-boot-starter-jdbc**| | |
|**spring-boot-starter-jersey**| | |
|**spring-boot-starter-jooq**| | |
|**spring-boot-starter-json**| | |
|**spring-boot-starter-jta-atomikos**| | |
|**spring-boot-starter-jta-bitronix**| | |
|**spring-boot-starter-jta-narayana**| | |
|**spring-boot-starter-mail**| | |
|**spring-boot-starter-mustache**| | |
|**spring-boot-starter-quartz**| | |
|**spring-boot-starter-security**|使用Spring Security的starter | |
|**spring-boot-starter-test**| | |
|**spring-boot-starter-thymeleaf**| 使用Thymeleaf视图构建MVC Web应用程序的starter| |
|**spring-boot-starter-validation**| 通过Hibernate Validator使用Java Bean验证的starter| |
|**spring-boot-starter-web**|使用Spring MVC构建Web的starter，包括RESTful应用程序。使用Tomcat作为默认的嵌入容器 | |
|**spring-boot-starter-web-services**| | |
|**spring-boot-starter-webflux**| | |
|**spring-boot-starter-websocket**| | |

除应用程序starter外，还可以使用以下starter来添加生产准备功能：

|名称|描述|Pom|
|---|----|-----|
|**spring-boot-starter-actuator**|Starter使用Spring Boot的执行器提供生产就绪功能，可帮助您监控和管理您的应用程序|[pom](https://github.com/spring-projects/spring-boot/tree/v2.0.1.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-actuator/pom.xml)|

最后，Spring Boot还包含以下启动器，如果您想要排除或交换特定技术方面，可以使用以下starter：

|名称|描述|Pom|
|---|----|-----|
|**spring-boot-starter-jetty**|将Jetty用作嵌入式servlet容器的starter。spring-boot-starter-tomcat的替代方案|[pom](https://github.com/spring-projects/spring-boot/tree/v2.0.1.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-jetty/pom.xml)|
|**spring-boot-starter-log4j2**|使用Log4j2用于日志的starter。spring-boot-starter-logging的替代方案|[pom](https://github.com/spring-projects/spring-boot/tree/v2.0.1.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-log4j2/pom.xml)|
|**spring-boot-starter-logging**|使用Logback进行日志记录的stareter。默认的日志starter|[pom](https://github.com/spring-projects/spring-boot/tree/v2.0.1.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-logging/pom.xml)|
|**spring-boot-starter-reactor-netty**|使用Reactor Netty作为嵌入式反应式HTTP服务器的starter。|[pom](https://github.com/spring-projects/spring-boot/tree/v2.0.1.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-reactor-netty/pom.xml)|
|**spring-boot-starter-tomcat**|使用Tomcat作为嵌入式servlet容器的starter。被spring-boot-starter-web所使用的默认servlet容器|[pom](https://github.com/spring-projects/spring-boot/tree/v2.0.1.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-tomcat/pom.xml)|
|**spring-boot-starter-undertow**|将Undertow 用作嵌入式servlet容器的starter。spring-boot-starter-tomcat的替代方案|[pom](https://github.com/spring-projects/spring-boot/tree/v2.0.1.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-undertow/pom.xml)|

>有关其他社区贡献者的列表，请参阅GitHub上的spring-boot-starters模块中的README文件。

## 组织你的代码
***
Spring Boot不需要任何特定的代码布局来工作。但是，有一些最佳实践可以提供帮助。
### 使用“默认”包
当一个类不包含一个包声明时，它被认为是在“默认包”中。通常不鼓励使用“默认包”，并且应该避免使用。对于使用**@ComponentScan**，**@EntityScan**或**@SpringBootApplication**注解的Spring Boot应用程序，它可能会导致特定问题，因为每个jar中的每个类都被读取。
> 我们建议您遵循Java推荐的软件包命名约定并使用反向域名（例如，com.example.project）。

### 定位主程序类
我们通常建议您将主应用程序类定位在其他类上方的根包中。 **@SpringBootApplication**注解通常放在您的主类上，它隐式地为特定项目定义了一个基本的“搜索包”。例如，如果您正在编写JPA应用程序，则使用**@SpringBootApplication**注解类的所在的包被用来搜索**@Entity**项目。使用根包也允许组件扫描仅适用于您的项目。

> 如果您不想使用**@SpringBootApplication**，则它导入的**@EnableAutoConfiguration**和@**ComponentScan**注解将定义该行为，以便您也可以使用该注解。

下面的清单显示了一个典型的布局：
```
com
 +- example
     +- myapplication
         +- Application.java
         |
         +- customer
         |   +- Customer.java
         |   +- CustomerController.java
         |   +- CustomerService.java
         |   +- CustomerRepository.java
         |
         +- order
             +- Order.java
             +- OrderController.java
             +- OrderService.java
             +- OrderRepository.java
```
**Application.java**文件将声明主方法以及基本的**@SpringBootApplication**，如下所示：
```java
package com.example.myapplication;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication

@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

}
```

## 配置类
***
Spring Boot支持基于Java的配置。虽然可以将**SpringApplication**与XML源一起使用，但我们通常建议您的主源是一个**@Configuration**类。通常，定义main方法的类作为主要**@Configuration**是一个很好的候选者。
> 许多使用XML配置的Spring配置示例已经被发布在互联网上。如果可能的话，请始终使用相等的基于Java的配置。搜索**Enable\***注解可能是一个很好的起点。

### 导入其他配置类
你不需要把你所有的**@Configuration**放到一个类中。 **@Import**注解可用于导入其他配置类。或者，您可以使用**@ComponentScan**自动获取所有Spring组件，包括**@Configuration**类。

### 导入XML配置
如果您绝对必须使用基于XML的配置，我们建议您仍以**@Configuration**类开始。然后您可以使用**@ImportResource**注解来加载XML配置文件。

## 自动配置
Spring Boot自动配置会尝试根据您添加的jar依赖自动配置您的Spring应用程序。例如，如果**HSQLDB**在您的类路径中，并且您尚未手动配置任何数据库连接Bean，则Spring Boot会自动配置一个内存数据库。

> 您应该只添加一个**@SpringBootApplication**或**@EnableAutoConfiguration**注解。我们通常建议您将一个或另一个添加到您的主要**@Configuration**类中。