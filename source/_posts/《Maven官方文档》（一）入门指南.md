title: 《Maven官方文档》（一）入门指南
id: 1498918423931
author: 不识
tags:
  - Maven
  - 构建工具
categories:
  - Maven
  - 入门
date: 2017-07-01 22:13:00
---
# Maven是什么
　　乍一看Maven有很多东西，但简而言之，Maven是一个通过在最佳实践的使用中提供一个明确的路径，将模式应用于项目的基础构建，用于促进理解和生产力的尝试。Maven实质上是一个项目管理与理解工具，它提供了一种帮助管理的方法：
- 构建（Builds）
- 文档（Documentation）
- 报表（Reporting）
- 依赖（Dependencies）
- SCMs
- 发布（Releases）
- 分布式（Distribution）

如果你想要了解Manver的更多背景信息，可以查阅[Maven的哲学](http://maven.apache.org/background/philosophy-of-maven.html)和[Maven的历史](http://maven.apache.org/background/history-of-maven.html)现在我们来看看用户如何从使用Maven中获益。
<!-- more -->

# 使用Maven对我的开发进程有怎么样好处
　　Maven通过采用标准的惯例和做法来加快您的开发周期，同时也帮助你实现更高的成功率，为你的构建进程提供诸多好处。

# 如何配置Maven
　　Maven的默认配置通常就足够了，但是如果你需要更改本地缓存，或者需要一个HTTP代理，你将需要创建配置。更多信息参阅“[Maven配置指南](http://maven.apache.org/guides/mini/guide-configuring-maven.html)”。
# 怎么创建我第一个Maven项目
　　我们要直接进入创建你的第一个Maven项目。要创建我们的第一个Maven项目，我们将使用Maven的原型机制。原型被定义为原始模式或模型，从该模式或模型中可以制作同一种类的其他所有东西。在Maven中，原型是项目的一个模板，它与一些用户输入相结合，以生成针对用户要求定制的Maven工作。我们将向您展示原型机制如何运作，但如果您想了解更多关于原型的信息，请参阅我们的“[原型简介](http://maven.apache.org/guides/introduction/introduction-to-archetypes.html)”。
　　现在创建你的第一个项目！为了创建最简单的Maven项目，请从命令行执行以下操作：
```bash
mvn -B archetype:generate \
  -DarchetypeGroupId=org.apache.maven.archetypes \
  -DgroupId=com.mycompany.app \
  -DartifactId=my-app
```
　　一旦你执行了这个命令，你会注意到一些事情发生了。首先，您将注意到，为新项目创建了一个名为my-app的目录，此目录包含一个名为pom.xml的文件，该文件应如下所示：
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>Maven Quick Start Archetype</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```
　　pom.xml包含此项目的项目对象模型（POM）。POM是Maven的基本工作单位。这点很重要，需要被牢记，因为Maven本质上是以项目为中心的，在这里一切都是围绕着一个项目的概念。简而言之，POM包含了关于你的项目的所有重要信息，并且它实质上是一个为查找与您的项目相关的任何内容一站式服务。明白了POM的重要性，我们推荐新用户参阅“[POM简介](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)”。

　　这是一个非常简单的POM，但仍然展示了每个POM包含的关键元素，所以让我们来看看每个元素，以熟悉POM的基本要素：
- **project** 这是所有Maven pom.xml文件中的顶级元素。
- **modelVersion** 这个元素指示了该POM正在使用的对象模型的版本。模型本身的版本很少变化，但是如果Maven开发人员认为有必要更改这个模型，则为了确保使用的稳定性，它是强制性的。
- **groupId** 此元素指示创建项目的组织或组的唯一标识符。groupId是项目的关键标识符之一，通常基于您组织的完全限定域名。例如，org.apache.maven.plugins是所有Maven插件的指定groupId。
- **artifactId**此元素指示此项目正在生成的主要artifact的唯一基础名称。项目的主要artifact通常是一个JAR文件。诸如源码包之类的辅助artifacts也使用artifactId作为其最终名称的一部分。由Maven生成的典型artifacts将具有&lt;artifactId> - &lt;version>.&lt;extension>（例如myapp-1.0.jar）的形式。
- **packaging** 此元素指示此artifact 使用的包类型（例如JAR，WAR，EAR等）。这不仅意味着如果生成的artifact是JAR,WAR,或者EAR，同时也指示了用作构建过程一部分的特定生命周期。（生命周期是我们将在指南中进一步讲解的一个话题。现在只需要记住，一个项目指示的packaging可以在定制构建生命周期中发挥作用。）packaging元素的默认值是JAR，所以你不必为大多数项目指定。
- **version** 该元素指示项目生成的artifact的版本。Maven在帮助您进行版本管理方面有很长的路要走，并且您会经常在version中看到SNAPSHOT指示符，这表明一个项目还处于开发状态。我们将在本指南中讨论[snapshots]()的使用及其进一步工作。
- **name** 此元素指示用于项目的显示名称。这通常用在Maven生成的文档中。
- **url** 这个元素指示可以在哪里找到该项目的地址。这通常用在Maven生成的文档中。
- **description** 该元素提供了您的项目的基本描述。这通常用在Maven生成的文档中。

有关可用于POM的元素的完整参考，请参阅我们的[“POM参考”](http://maven.apache.org/ref/current/maven-model/maven.html)。现在让我们回到手头的项目。

　　在你的第一个项目的原型生成后，你会注意到以下的目录结构被创建。
```
my-app
|-- pom.xml
`-- src
    |-- main
    |   `-- java
    |       `-- com
    |           `-- mycompany
    |               `-- app
    |                   `-- App.java
    `-- test
        `-- java
            `-- com
                `-- mycompany
                    `-- app
                        `-- AppTest.java

```
　　如您所见，从原型创建的项目有一个POM，一个应用程序源的源代码树和一个测试源的源代码树。这是Maven项目的标准布局（应用程序源代码在“${basedir}/src/main/java”中，测试源代码在“ ${basedir}/src/test/java”中，其中“${basedir}”表示包含了pom.xml的目录）。
　　如果要手动创建一个Maven项目，这是我们建议使用的目录结构。这是Maven惯例，要了解更多信息，您可以阅读我们的[“标准目录布局简介”](http://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html)。
　　现在我们有一个POM，一些应用程序源码和一些测试源码，你可能会问...

# 如何编译我的程序源码？
　　切换到由原型创建的pom.xml所在的目录：生成并执行以下命令来编译程序源码：
```
mvn compile
```
执行该命令后，您将看到如下所示的输出：
```
[INFO] ----------------------------------------------------------------------------
[INFO] Building Maven Quick Start Archetype
[INFO]    task-segment: [compile]
[INFO] ----------------------------------------------------------------------------
[INFO] artifact org.apache.maven.plugins:maven-resources-plugin: \
  checking for updates from central
...
[INFO] artifact org.apache.maven.plugins:maven-compiler-plugin: \
  checking for updates from central
...
[INFO] [resources:resources]
...
[INFO] [compiler:compile]
Compiling 1 source file to <dir>/my-app/target/classes
[INFO] ----------------------------------------------------------------------------
[INFO] BUILD SUCCESSFUL
[INFO] ----------------------------------------------------------------------------
[INFO] Total time: 3 minutes 54 seconds
[INFO] Finished at: Fri Sep 23 15:48:34 GMT-05:00 2005
[INFO] Final Memory: 2M/6M
[INFO] ----------------------------------------------------------------------------
```
　　第一次执行这个（或任何其他）命令时，Maven将需要下载其完成命令所需的所有插件和相关依赖项。对于一个简洁安装的Maven，这可能需要相当长的一段时间（在上面的输出中，它花了差不多4分钟）。如果再次执行该命令，Maven现在已经有了它所需的东西，因此不需要下载任何新内容，并能够更快地执行命令。  
　　从输出可以看出，编译的类被放在$ {basedir} / target / classes中，这是Maven使用的另一个标准惯例。所以，如果你是一个敏锐的观察者，你会注意到，通过使用标准约定，上面的POM非常小并且你不必明确地告诉Maven你的任何源代码在哪里或者应该输出到哪里。通过遵循标准的Maven约定，您可以用很少的精力完成很多工作！就做一个随意的比较，让我们来看下要想完成相同的事情，在Ant中你必须需要做哪些事。      
　　现在这只是简单编译一个程序源代码树，所以显示的Ant脚本和上面显示的POM大小大概相同。但是，我们还可以看到我们可以只使用这个简单的POM做多少事情！
# 如何编译测试源码并运行我的单元测试？
　　现在，您正在成功地编译程序的源代码，现在您已经有了一些要编译和执行的单元测试（因为每个程序员总是编写和执行单元测试）   
执行以下命令
```
mvn test
```
执行该命令后，您将看到如下所示的输出：
```
[INFO] ----------------------------------------------------------------------------
[INFO] Building Maven Quick Start Archetype
[INFO]    task-segment: [test]
[INFO] ----------------------------------------------------------------------------
[INFO] artifact org.apache.maven.plugins:maven-surefire-plugin: \
  checking for updates from central
...
[INFO] [resources:resources]
[INFO] [compiler:compile]
[INFO] Nothing to compile - all classes are up to date
[INFO] [resources:testResources]
[INFO] [compiler:testCompile]
Compiling 1 source file to C:\Test\Maven2\test\my-app\target\test-classes
...
[INFO] [surefire:test]
[INFO] Setting reports dir: C:\Test\Maven2\test\my-app\target/surefire-reports
 
-------------------------------------------------------
 T E S T S
-------------------------------------------------------
[surefire] Running com.mycompany.app.AppTest
[surefire] Tests run: 1, Failures: 0, Errors: 0, Time elapsed: 0 sec
 
Results :
[surefire] Tests run: 1, Failures: 0, Errors: 0
 
[INFO] ----------------------------------------------------------------------------
[INFO] BUILD SUCCESSFUL
[INFO] ----------------------------------------------------------------------------
[INFO] Total time: 15 seconds
[INFO] Finished at: Thu Oct 06 08:12:17 MDT 2005
[INFO] Final Memory: 2M/8M
[INFO] ----------------------------------------------------------------------------
```
　　关于输出有些事情需要注意：
- Maven这时下载更多的依赖。这些是执行测试所必需的依赖和插件（用于编译所需的依赖已经存在，所以就不会再次下载）
- 在编译和执行测试之前，Maven会编译主代码（因为所有这些类都是最新的，因为自从上次编译后没有进行任何的修改）

如果您只想编译测试源码（但不执行测试），则可以执行以下操作：
```
mvn test--compile
```
　　现在，您可以编译程序源码，编译测试并执行测试，您会想要继续下一个逻辑步骤，所以您会问...
# 如何创建一个JAR并且安装到我的本地库
　　创建一个JAR是相当简单直接，可以通过执行下面命令来完成这项工作
```
mvn package
```
　　如果您查看项目的POM，您会注意到packaging元素被设置为jar。这是上面命令中Maven如何知道要生成的是JAR文件（稍后我们将讲解更多）。您现在可以查看$ {basedir} / target目录，您将看到生成的JAR文件。
　　现在你会想将已经生成的artifact（该JAR文件）安装到你的本地库（默认位置是${user.home}/.m2/repository）。有关库的更多信息，请参阅我们的[“库简介”](http://maven.apache.org/guides/introduction/introduction-to-repositories.html)但我们继续安装我们的artifact！为此执行以下命令：
```
mvn install
```
执行此命令后，您将看到以下输出：
```
[INFO] ----------------------------------------------------------------------------
[INFO] Building Maven Quick Start Archetype
[INFO]    task-segment: [install]
[INFO] ----------------------------------------------------------------------------
[INFO] [resources:resources]
[INFO] [compiler:compile]
Compiling 1 source file to <dir>/my-app/target/classes
[INFO] [resources:testResources]
[INFO] [compiler:testCompile]
Compiling 1 source file to <dir>/my-app/target/test-classes
[INFO] [surefire:test]
[INFO] Setting reports dir: <dir>/my-app/target/surefire-reports
 
-------------------------------------------------------
 T E S T S
-------------------------------------------------------
[surefire] Running com.mycompany.app.AppTest
[surefire] Tests run: 1, Failures: 0, Errors: 0, Time elapsed: 0.001 sec
 
Results :
[surefire] Tests run: 1, Failures: 0, Errors: 0
 
[INFO] [jar:jar]
[INFO] Building jar: <dir>/my-app/target/my-app-1.0-SNAPSHOT.jar
[INFO] [install:install]
[INFO] Installing <dir>/my-app/target/my-app-1.0-SNAPSHOT.jar to \
   <local-repository>/com/mycompany/app/my-app/1.0-SNAPSHOT/my-app-1.0-SNAPSHOT.jar
[INFO] ----------------------------------------------------------------------------
[INFO] BUILD SUCCESSFUL
[INFO] ----------------------------------------------------------------------------
[INFO] Total time: 5 seconds
[INFO] Finished at: Tue Oct 04 13:20:32 GMT-05:00 2005
[INFO] Final Memory: 3M/8M
[INFO] ----------------------------------------------------------------------------
```
　　请注意，surefire插件（它会执行测试）会查找包含在具有特定命名约定的文件中的测试。默认测试包括：
- \*\*/\*Test.java
- \*\*/Test\*.java
- \*\*/\*TestCase.java 

默认排除的是：
- \*\*/Abstract\*Test.java
- \*\*/Abstract\*TestCase.java

　　您已经完成了建立，构建，测试，打包和安装典型Maven项目的过程。这可能是使用Maven的项目要做的绝大多数工作，并且如果您注意到，您所能做的一切都是由一个被称为项目模型或者POM的18行文件驱动的。如果您看到一个提供了我们迄今为止所实现的相同功能的典型的Ant构建文件，您会发现它已经是POM的两倍大小，而且我们刚刚开始！Maven还有更多可用的功能，而且不需要对我们目前的POM做额外的补充。要想获得超出我们示例更多的功能，Ant构建文件必须作出很多容易出错的补充。    
　　那么还有什么可以免费获得的？还有大量的Maven插件，即使是我们以上的简单POM，也可以开箱即用。我们在这里特别提到一个，因为它是Maven非常珍贵的功能之一：不需要任何工作，您的这个POM有足够的信息为您的项目生成一个网站！你最可能想要定制你的Maven网站，但是如果你时间紧迫的话，所有你需要做的只是执行以下命令，来提供关于你的项目基本信息。
```
mvn site
```
还有很多其他可以执行的独立goal，例如：
```
mvn clean
```
这将在启动之前删除所有构建数据的target目录，以便它是最新的。     
也许你想为项目生成一个IntelliJ IDEA描述符？
```
mvn idea:idea
```
这可以运行在以前的IDEA项目的顶部 - 它会更新设置，而不是重新开始。   
如果您正在使用Eclipse IDE，只需调用：

```
mvn eclipse:eclipse
```
 

> **注意**：Maven 1.0中的一些熟悉的goal依然存在，比如jar：jar，但他们可能不会像你所期望的那样行事。目前，jar：jar不会重新编译源代码 - 它只是简单地从target/classes目录创建一个JAR，这是在其他的一切工作已经完成的前提下。

# 什么是SNAPSHOT版本？

注意在pom.xml文件中version标签的值如下显示，它有一个后缀：-SNAPSHOT。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  ...>
  <groupId>...</groupId>
  <artifactId>my-app</artifactId>
  ...
  <version>1.0-SNAPSHOT</version>
  <name>Maven Quick Start Archetype</name>
  ...
</project>
```
　　SNAPSHOT值是指开发分支的“最新”代码，它并不保证代码稳定或不再改变。相反，“release”版本中的代码（任何没有SNAPSHOT后缀的版本值）都是不再变更的。 
　　换句话说，SNAPSHOT版本是最终“发布”版本之前的“开发”版本。SNAPSHOT要比它的发布版本老。    
　　在发布过程中，x.y-SNAPSHOT的版本更改为x.y.发布过程还将开发版本增加到x.(y + 1)-SNAPSHOT。例如，版本1.0-SNAPSHOT被发布为版本1.0，新的开发版本是版本1.1-SNAPSHOT。

# 如何使用插件

　　无论何时要自定义Maven项目的构建，都可以通过添加或重新配置插件来实现。  
　　**Maven 1.0用户注意事项**：在Maven 1.0中，您需要向maven.xml添加一些preGoal，并将一些条目添加到project.properties中。这里有点不同。
　　比如这个例子，我们会配置Java编译器来允许使用JDK5.0源码。只需要简单的将以下添加到你的POM中：
```xml
...
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.3</version>
      <configuration>
        <source>1.5</source>
        <target>1.5</target>
      </configuration>
    </plugin>
  </plugins>
</build>
...
```
　　你会注意到，Maven中的所有插件看起来都像一个依赖 - 在某些方面它们确实是。这个插件将被自动下载并使用 - 包括一个特定的版本，如果你要求它的话（默认是使用最新可用的）。  
　　configuration元素将给定的参数应用于编译器插件的每个goal上。在上述情况下，编译器插件已经被用作构建过程的一部分，这只是改变了配置。还可以为流程添加新goal，并配置特定goal。有关信息，请参阅[“构建生命周期简介”](http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)。     
　　要想了解插件有哪些可用配置，你可以查阅插件列表，并浏览你正在使用的插件和goal。有关如何配置插件的可用参数的通用信息，请参阅[“配置插件指南”](http://maven.apache.org/guides/mini/guide-configuring-plugins.html)。

# 如何向JAR添加资源？

　　另一个可以满足的常见用例是没有更改我们上面的POM而将资源打包进了JAR文件。对于这个常见的任务，Maven再次依赖于标准目录布局，意味着通过使用标准的Maven约定，您可以仅仅通过将这些资源放进一个标准目录结构中，就可以资源打包进JAR文件。    
　　您在下面的示例中看到，我们添加了目录${basedir}/src/main/resources，可以将我们希望在JAR中打包的任何资源放入其中。Maven采用的简单规则是：${basedir}/src/ main/resources目录中放置的任何目录或文件都会在以JAR基础上开始的完全相同的结构打包进JAR内。
```
my-app
|-- pom.xml
`-- src
    |-- main
    |   |-- java
    |   |   `-- com
    |   |       `-- mycompany
    |   |           `-- app
    |   |               `-- App.java
    |   `-- resources
    |       `-- META-INF
    |           `-- application.properties
    `-- test
        `-- java
            `-- com
                `-- mycompany
                    `-- app
                        `-- AppTest.java
```
　　所以在我们的示例中可以看到我们有一个META-INF目录，该目录中包含一个application.properties文件。如果您解压缩Maven为您创建的JAR，并查看它，您将看到以下内容：
```
|-- META-INF
|   |-- MANIFEST.MF
|   |-- application.properties
|   `-- maven
|       `-- com.mycompany.app
|           `-- my-app
|               |-- pom.properties
|               `-- pom.xml
`-- com
    `-- mycompany
        `-- app
            `-- App.class
```
　　您可以看到，${basedir}/src/main/resources的内容可以从JAR的基础开始，我们的application.properties文件位于META-INF目录中。你还会注意到一些其他文件，比如META-INF/MANIFEST.MF以及一个pom.xml和pom.properties文件. 这些是Maven中JAR的标准生成的。如果你选择的话可以创建你自己的清单，但是如果你没有创建的话Maven会默认生成一个（你可以修改默认清单中的实体。稍后我们会介绍）。pom.xml和pom.propertities文件被打包进JAR中，这样每个Maven产生的artifact都是可以自我描述，并且可以允许你在有需要时利用自己的应用程序的元数据。一个简单的用法可能是获取您的应用程序的版本。操作POM文件可能需要你使用一些Maven实用程序，但是使用标准的Java API利用这些属性，如下所示
```
#Generated by Maven
#Tue Oct 04 15:43:21 GMT-05:00 2005
version=1.0-SNAPSHOT
groupId=com.mycompany.app
artifactId=my-app
```
　　要为您的单元测试添加资源到类路径下，你遵循与向JAR添加资源相同的模式，除了放置资源的目录是${basedir}/src/test/resources之外。此时，您将有一个项目目录结构，如下所示：
```
my-app
|-- pom.xml
`-- src
    |-- main
    |   |-- java
    |   |   `-- com
    |   |       `-- mycompany
    |   |           `-- app
    |   |               `-- App.java
    |   `-- resources
    |       `-- META-INF
    |           |-- application.properties
    `-- test
        |-- java
        |   `-- com
        |       `-- mycompany
        |           `-- app
        |               `-- AppTest.java
        `-- resources
            `-- test.properties
```
在单元测试中，您可以使用简单的代码片段来访问以下测试所需的资源：
```java
...
 
// Retrieve resource
InputStream is = getClass().getResourceAsStream( "/test.properties" );
 
// Do something with the resource
 
...
```
# 如何筛选资源文件？
　　有时一个资源文件会需要包含一个只能在构建时提供的值。要在Maven中完成此操作，请使用语法${&lt;property name>}将包含该值的属性引用到你的资源文件中。该属性可以是您的pom.xml中定义的值之一，一个定义在用户的settings.xml中的值，一个定义在外部属性文件的值，或者一个系统属性。   
　　要在复制时让Maven筛选资源，只需将pom.xml中的资源目录的filtering设置为true即可：
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>
 
  <name>Maven Quick Start Archetype</name>
  <url>http://maven.apache.org</url>
 
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
 
  <build>
    <resources>
      <resource>
        <directory>src/main/resources</directory>
        <filtering>true</filtering>
      </resource>
    </resources>
  </build>
</project>
```
　　你会注意到，我们需要添加之前没有的build，resources和resource元素。除此之外，我们还需要明确指出资源位于src/main/resources目录中。所有这些信息之前都被当作默认值提供，但是因为filtering的默认值是false，所以我们需要自己将其添加到pom.xml中，以用来覆盖该默认值设置filtering为true。    
　　要引用一个定义在你的pom.xml中的属性，该属性名称要使用定义值的XML元素的名称，“pom”被运行作为project（root）元素的别名。所以$ {project.name}指的是项目的名称，$ {project.version}是指项目的版本，$ {project.build.finalName}是指在构建项目打包时产生的文件的最终名称，等等。需要注意，POM的某些元素有默认值，所以不必明确的在你的POM中定义这些可用的值。类似地，可以使用以“settings”开头的属性名称来引用用户的settings.xml中的值（例如，$ {settings.localRepository}是指用户本地仓库的路径）。      
　　继续我们的例子，让我们将一些属性添加到application.properties文件中（我们放在src/main/resources目录中），当资源被过滤时，其值将被提供：
```
# application.properties
application.name=${project.name}
application.version=${project.version}
```
到了这一步，您可以执行以下命令（process-resources是资源被复制和过滤的构建生命周期阶段）：
```
mvn process-resources
```
并且target/classes下的application.properties文件（最终将进入该jar）如下所示：  
 ```
 # application.properties
application.name=Maven Quick Start Archetype
application.version=1.0-SNAPSHOT
```
 要引用外部文件中定义的属性，您需要做的是在pom.xml中添加对该外部文件的引用。首先，我们创建我们的外部属性文件，并将其称为src/main/filters/filter.properties：
```
# filter.properties
my.filter.value=hello!
```
接下来，我们要在pom.xml中添加这个新文件的引用：
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>
 
  <name>Maven Quick Start Archetype</name>
  <url>http://maven.apache.org</url>
 
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
 
  <build>
    <filters>
      <filter>src/main/filters/filter.properties</filter>
    </filters>
    <resources>
      <resource>
        <directory>src/main/resources</directory>
        <filtering>true</filtering>
      </resource>
    </resources>
  </build>
</project>
```
然后，如果我们在application.properties文件中添加对该属性的引用：
```
# application.properties
application.name=${project.name}
application.version=${project.version}
message=${my.filter.value}
```
　　在下面mvn process-resources命令的执行中会将我们的新的属性值放入application.properties中。作为在外部文件中定义my.filter.value属性的替代方法，您还可以在pom.xml的properties部分中定义它，您将获得相同的效果（注意，我也不需要对src / main/filters/filter.properties的引用）；
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>
 
  <name>Maven Quick Start Archetype</name>
  <url>http://maven.apache.org</url>
 
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
 
  <build>
    <resources>
      <resource>
        <directory>src/main/resources</directory>
        <filtering>true</filtering>
      </resource>
    </resources>
  </build>
 
  <properties>
    <my.filter.value>hello</my.filter.value>
  </properties>
</project>
```
过滤资源也可以从系统属性获取值;内置于Java（如java.version或user.home）中的系统属性或使用标准Java -D参数在命令行中定义的属性。要继续这个例子，我们来更改我们的application.properties文件，如下所示：
```
# application.properties
java.version=${java.version}
command.line.prop=${command.line.prop}
```
现在，当您执行以下命令（注意命令行上的command.line.prop属性的定义）时，application.properties文件将包含系统属性中的值。

# 如何使用外部依赖？
　　你可能已经注意到我们已经在POM中使用了一个dependencies元素作为示例。事实上，你一直在使用一个外部的依赖，但是在这里我们将会详细介绍一下这个的工作原理。有关更全面的介绍，请参阅我们的[“依赖机制介绍”](http://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html)。       
　　pom.xml的dependencies部分列举我们的项目需要构建的所有外部依赖（无论是在编译时期，测试时期，允许时期或者什么时候的依赖）。现在，我们的项目只依赖于JUnit（为了清楚起见，我拿出了所有资源过滤的东西）
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>
 
  <name>Maven Quick Start Archetype</name>
  <url>http://maven.apache.org</url>
 
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```
　　对于每个外部依赖，你会需要至少定义四样东西：groupId,artifactId,version以及scope。groupId，artifactId和version与构建该依赖项目的pom.xml中给出的相同。scope元素指示您的项目如何使用该依赖关系，它的值可以是compile，test和runtime。有关可以为依赖项指定的所有内容的更多信息，请参阅[“项目描述符参考”](http://maven.apache.org/ref/current/maven-model/maven.html)。 
　　有关依赖机制整体的更多信息，请参阅[“依赖机制简介”](http://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html)。      
　　通过有关依赖的信息，Maven将在构建项目时引用依赖。Maven在哪里引用依赖关系呢？Maven在您的本地仓库（默认位置是${user.home}/.m2/repository）中查找所有依赖项。在上一节中，我们将项目（my-app-1.0-SNAPSHOT.jar）中的artifact安装到本地仓库中。一旦它被安装在那里，另一个项目可以通过将依赖信息添加到它的pom.xml来引用该jar作为依赖：
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-other-app</artifactId>
  ...
  <dependencies>
    ...
    <dependency>
      <groupId>com.mycompany.app</groupId>
      <artifactId>my-app</artifactId>
      <version>1.0-SNAPSHOT</version>
      <scope>compile</scope>
    </dependency>
  </dependencies>
</project>
```
　　其他地方的依赖怎么使用呢？如果将它们获取到我的本地仓库？每当项目引用本地仓库中不可用的依赖项时，Maven将从远程仓库下载依赖项到本地仓库。当您构建您的第一个项目时，您可能已经注意到Maven下载了很多东西（这些下载是用于构建项目的各种插件的依赖）。默认情况下，Maven使用的远程仓库可以在http://repo.maven.apache.org/maven2/中找到（并浏览）。您还可以设置自己的远程仓库（可能是您公司的中央仓库），来代替或补充默认的远程仓库。有关仓库的更多信息，请参阅[“仓库简介”](http://maven.apache.org/guides/introduction/introduction-to-repositories.html)。    
　　让我们在我们的项目中添加其他的依赖项。假设我们添加了一些日志记录到代码中，需要添加log4j作为依赖。首先，我们需要知道log4j的groupId，artifactId和version是什么。我们可以浏览ibiblio并寻找它，或者通过使用Google来帮助搜索“site：www.ibiblio.org maven2 log4j”。搜索显示一个名为/maven2/log4j/log4j（或/pub/packages/maven2/log4j/log4j）的目录。该目录有一个名为maven-metadata.xml的文件。以下是log4j的maven-metadata.xml的内容：
```xml
<metadata>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>1.1.3</version>
  <versioning>
    <versions>
      <version>1.1.3</version>
      <version>1.2.4</version>
      <version>1.2.5</version>
      <version>1.2.6</version>
      <version>1.2.7</version>
      <version>1.2.8</version>
      <version>1.2.11</version>
      <version>1.2.9</version>
      <version>1.2.12</version>
    </versions>
  </versioning>
</metadata>
```
　　从这个文件中我们可以看到我们想要的groupId是“log4j”，而artifactId是“log4j”。我们可以看到很多不同的版本值可供选择;现在，我们将使用最新版本1.2.12（一些maven-metadata.xml文件也可以指定哪一个是当前发布版本）。除了maven-metadata.xml文件，我们可以看到对应每个版本的log4j库的目录。在每个这些文件中，我们将找到实际的jar文件（例如log4j-1.2.12.jar）以及一个pom文件（这是依赖项的pom.xml，表明它可能有其他依赖关系和一些其他信息）和另一个maven-metadata.xml文件。还有一个与这些文件相对应的md5文件，其中包含这些文件的MD5哈希值。您可以使用它来验证库，或找出您可能正在使用的特定库是哪个版本。    
　　现在我们知道我们需要的信息，我们可以将依赖项添加到我们的pom.xml中：
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>
 
  <name>Maven Quick Start Archetype</name>
  <url>http://maven.apache.org</url>
 
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.12</version>
      <scope>compile</scope>
    </dependency>
  </dependencies>
</project>
```
现在，当我们编译项目（mvn compile）时，我们将看到Maven为我们下载log4j依赖项。
# 如何在我的远程仓库中部署我的jar？

要将jar部署到外部仓库，你必须在pom.xml中配置仓库的url以及在settting.xml中配置用来连接仓库的验证信息。   
以下是使用scp和username / password认证的示例：
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>
 
  <name>Maven Quick Start Archetype</name>
  <url>http://maven.apache.org</url>
 
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.apache.codehaus.plexus</groupId>
      <artifactId>plexus-utils</artifactId>
      <version>1.0.4</version>
    </dependency>
  </dependencies>
 
  <build>
    <filters>
      <filter>src/main/filters/filters.properties</filter>
    </filters>
    <resources>
      <resource>
        <directory>src/main/resources</directory>
        <filtering>true</filtering>
      </resource>
    </resources>
  </build>
  <!--
   |
   |
   |
   -->
  <distributionManagement>
    <repository>
      <id>mycompany-repository</id>
      <name>MyCompany Repository</name>
      <url>scp://repository.mycompany.com/repository/maven2</url>
    </repository>
  </distributionManagement>
</project>
```
```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                      http://maven.apache.org/xsd/settings-1.0.0.xsd">
  ...
  <servers>
    <server>
      <id>mycompany-repository</id>
      <username>jvanzyl</username>
      <!-- Default value is ~/.ssh/id_dsa -->
      <privateKey>/path/to/identity</privateKey> (default is ~/.ssh/id_dsa)
      <passphrase>my_key_passphrase</passphrase>
    </server>
  </servers>
  ...
</settings>
```
请注意，如果你连接到一个将sshd\_confing中的“PasswordAuthentication”参数设置为“no”的openssh ssh服务器,，您都必须为每次用户名/密码认证输入密码（虽然您可以通过输入用户名和密码登录使用另一个ssh客户端）在这种情况下，您可能需要切换到公钥认证。   
在settings.xml中使用密码应该小心。有关详细信息，请参阅[“密码加密”](http://maven.apache.org/guides/mini/guide-encryption.html)。    
# 如何创建文档？
为了让您从Maven的文档系统开始，您可以使用原型机制来使用以下命令为现有项目生成一个站点：
```
mvn archetype:generate \
  -DarchetypeGroupId=org.apache.maven.archetypes \
  -DarchetypeArtifactId=maven-archetype-site \
  -DgroupId=com.mycompany.app \
  -DartifactId=my-app-site
```
现在转到[“创建一个网站的指南”](http://maven.apache.org/guides/mini/guide-site.html)，了解如何为您的项目创建文档。

# 如何建立其他类型的项目？   
请注意，生命周期适用于任何项目类型。例如，在基本目录中，我们可以创建一个简单的Web应用程序：
```
mvn archetype:generate \
    -DarchetypeGroupId=org.apache.maven.archetypes \
    -DarchetypeArtifactId=maven-archetype-webapp \
    -DgroupId=com.mycompany.app \
    -DartifactId=my-webapp
```
请注意，这些都必须在单独的一行上。这将创建一个名为my-webapp的目录，其中包含以下项目描述符：
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-webapp</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>
 
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
 
  <build>
    <finalName>my-webapp</finalName>
  </build>
</project>
```
注意&lt;packaging>元素-它告诉Maven以WAR形式构建。Change into the webapp project's directory and try:
中文(简体)
切换到webapp项目的目录，然后尝试：
```
mvn clean package
```
你可以看到target/my-webapp.war被构建，并且所有正常步骤都已执行。
# 如何一次构建多个项目？
处理多个模块的概念被内置于Maven。在本节中，我们将展示如何构建上述的WAR，并在一步中包含以前的JAR。   
首先，我们需要在另外两个目录上面添加一个父pom.xml文件，所以应该是这样的：
```
+- pom.xml
+- my-app
| +- pom.xml
| +- src
|   +- main
|     +- java
+- my-webapp
| +- pom.xml
| +- src
|   +- main
|     +- webapp
```
您将创建的POM文件应包含以下内容：
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>app</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>pom</packaging>
 
  <modules>
    <module>my-app</module>
    <module>my-webapp</module>
  </modules>
</project>
```
我们在webapp中需要一个JAR依赖项，所以将其添加到my-webapp/pom.xml中：
```xml
 ...
  <dependencies>
    <dependency>
      <groupId>com.mycompany.app</groupId>
      <artifactId>my-app</artifactId>
      <version>1.0-SNAPSHOT</version>
    </dependency>
    ...
  </dependencies>
```
最后，将以下&lt;parent>元素添加到子目录中的其他pom.xml文件中：
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <parent>
    <groupId>com.mycompany.app</groupId>
    <artifactId>app</artifactId>
    <version>1.0-SNAPSHOT</version>
  </parent>
  ...
```
现在，从顶级目录尝试它，运行：
```
mvn clean install
```
WAR现在已经在my-webapp/target/my-webapp.war中创建，并且JAR包含在内：
```
$ jar tvf my-webapp/target/my-webapp-1.0-SNAPSHOT.war
   0 Fri Jun 24 10:59:56 EST 2005 META-INF/
 222 Fri Jun 24 10:59:54 EST 2005 META-INF/MANIFEST.MF
   0 Fri Jun 24 10:59:56 EST 2005 META-INF/maven/
   0 Fri Jun 24 10:59:56 EST 2005 META-INF/maven/com.mycompany.app/
   0 Fri Jun 24 10:59:56 EST 2005 META-INF/maven/com.mycompany.app/my-webapp/
3239 Fri Jun 24 10:59:56 EST 2005 META-INF/maven/com.mycompany.app/my-webapp/pom.xml
   0 Fri Jun 24 10:59:56 EST 2005 WEB-INF/
 215 Fri Jun 24 10:59:56 EST 2005 WEB-INF/web.xml
 123 Fri Jun 24 10:59:56 EST 2005 META-INF/maven/com.mycompany.app/my-webapp/pom.properties
  52 Fri Jun 24 10:59:56 EST 2005 index.jsp
   0 Fri Jun 24 10:59:56 EST 2005 WEB-INF/lib/
2713 Fri Jun 24 10:59:56 EST 2005 WEB-INF/lib/my-app-1.0-SNAPSHOT.jar
```
它是怎么工作的呢？首先，父POM被创建（称作app），它有一个pom包和定义的模块列表。这告诉Maven运行所有的项目，而不仅仅是当前的项目（要覆盖此行为，可以使用--non-recursive命令行选项）。     
接下来，我们告诉WAR它需要my-app的JAR。这做了一些事情：它使得它在类路径中可用于WAR中的任何代码（在这种情况下都不是），它确保JAR始终在WAR之前构建，并且它向WAR插件指示将JAR包括在其库目录。 您可能已经注意到，junit-4.11.jar是依赖依赖项，但并没有在WAR中结束。原因是&lt;scope>test&lt;/scope>元素 - 它只需要用来进行测试，因此它不像依赖项my-app一样在编译时期被包含在web应用程序中。

最后一步包括一个父定义。这与您可能从Maven 1.0熟悉的extend元素不同：即使项目与它的父级分开分布，这样可以通过在仓库中查找以确保POM始终可以被定位。
与Maven 1.0不同，它不需要你允许install来成功执行这些步骤-你可以在它自己上运行package并且生成的artifact会从target目录使用，而不是本地仓库。       
您可能希望从顶层目录再次生成您的IDEA工作区
```
mvn idea:idea
```