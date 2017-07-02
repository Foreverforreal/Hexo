title: 《Maven官方文档》（一）入门指南
id: 1498918423931
author: 不识
tags:
  - Maven
  - 构建工具
categories:
  - Maven
date: 2017-07-01 22:13:00
---
# Maven是什么
乍一看Maven有很多东西，但简而言之，Maven是一个在最佳实践的使用中通过提供一个明确的途径，尝试将模式应用于项目的构建基础设施来促进理解和生产力。Maven实质上是一个项目管理与理解工具，它提供了一种帮助管理的方法：
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
- **artifactId**此元素指示此项目正在生成的主要artifact的唯一基础名称。项目的主要artifact通常是一个JAR文件。诸如源码包之类的辅助artifacts也使用artifactId作为其最终名称的一部分。由Maven生成的典型artifacts将具有&lt;artifactId> - &lt;version>。&lt;extension>（例如myapp-1.0.jar）的形式。
- **packaging** 此元素指示此artifact 使用的包类型（例如JAR，WAR，EAR等）。这不仅意味着如果生成的artifact是JAR,WAR,或者EAR，同时也指示了用作构建过程一部分的特定生命周期。（生命周期是我们将在指南中进一步讲解的一个话题。现在只需要记住，一个项目指示的packaging可以在定制构建生命周期中发挥作用。）packaging元素的默认值是JAR，所以你不必为大多数项目指定。
- **version** 该元素指示项目生成的artifact的版本。Maven在帮助您进行版本管理方面有很长的路要走，并且您会经常在version中看到SNAPSHOT指示符，这表明一个项目还处于发展状态。我们将在本指南中讨论[snapshots]()的使用及其进一步工作。
- **name** 此元素指示用于项目的显示名称。这通常用在Maven生成的文档中。
- **url** 这个元素指示可以在哪里找到该项目的地址。这通常用在Maven生成的文档中。
- **description** 该元素提供了您的项目的基本描述。这通常用在Maven生成的文档中。

有关可用于POM的元素的完整参考，请参阅我们的[POM参考](http://maven.apache.org/ref/current/maven-model/maven.html)。现在让我们回到手头的项目。

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
如果要手动创建一个Maven项目，这是我们建议使用的目录结构。这是Maven惯例，要了解更多信息，您可以阅读我们的[标准目录布局简介](http://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html)。
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
现在，您正在成功地编译程序的源代码，现在您已经有了一些要编译和执行的单元测试
（因为每个程序员总是编写和执行单元测试）   
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

您已经完成了建立，构建，测试，打包和安装典型Maven项目的过程。这可能是使用Maven的项目要做的绝大多数工作，并且如果您注意到，您所能做的一切都是由一个被称为项目模型或者POM的18行文件驱动的。如果您看到一个提供了我们迄今为止所实现的相同功能的典型的Ant构建文件，您会发现它已经是POM大小的两倍，而且我们刚刚开始！Maven还有更多可用的功能，而且不需要对我们目前的POM做额外的补充。要想获得超出我们示例更多的功能，Ant构建文件必须作出很多容易出错的补充。    
那么还有什么可以免费获得的？还有大量的Maven插件，即使是我们以上的简单POM，也可以开箱即用。我们在这里特别提到一个，因为它是Maven非常珍贵的功能之一：不需要任何工作，您的这个POM有足够的信息为您的项目生成一个网站！你最可能想要定制你的Maven网站，但是如果你时间紧迫的话，所有你需要做的只是执行以下命令，来提供关于你的项目基本信息。
```
mvn site
```
还有很多其他可以执行的独立goals，例如：
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
>**注意**：Maven 1.0中的一些熟悉的goals依然存在，比如jar：jar，但他们可能不会像你所期望的那样行事。目前，jar：jar不会重新编译源代码 - 它只是简单地从target/classes目录创建一个JAR，这是在其他的一切工作已经完成的前提下。

# 什么是SNAPSHOT版本？

注意在pom.xml文件中version标签的值如下显示，它有一个后缀：-SNAPSHOT.
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  ...
  <groupId>...</groupId>
  <artifactId>my-app</artifactId>
  ...
  <version>1.0-SNAPSHOT</version>
  <name>Maven Quick Start Archetype</name>
  ...
```
SNAPSHOT值是指开发分支的“最新”代码，它并不保证代码稳定或不再改变。相反，“release”版本中的代码（任何没有SNAPSHOT后缀的版本值）都是不再更的。 
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
你会注意到，Maven中的所有插件看起来都像一个依赖 - 在某些方面它们确实是。这个插件将被自动下载并使用 - 包括一个特定的版本，如果你要求它（默认是使用最新可用的）。  
configuration元素将给定的参数应用于编译器插件的每个goal上。在上述情况下，编译器插件已经被用作构建过程的一部分，这只是改变了配置。还可以为流程添加新goal，并配置特定goal。有关信息，请参阅[“构建生命周期简介”](http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)。     
要想了解插件有哪些可用配置，你可以查阅插件列表，并浏览你正在使用的插件和goal。有关如何配置插件的可用参数的通用信息，请参阅[“配置插件指南”](http://maven.apache.org/guides/mini/guide-configuring-plugins.html)。

# 如何向JAR添加资源？

另一个可以满足的常见用例是没有更改我们上面的POM而将资源打包进了JAR文件。对于这个常见的任务，Maven再次依赖于标准目录布局，意味着通过使用标准的Maven约定，您可以仅仅通过将这些资源放进一个标准目录结构中，就可以资源打包进JAR文件。    
您在下面的示例中看到，我们添加了目录$ {basedir} / src / main / resources，可以将我们希望在JAR中打包的任何资源放入其中。Maven采用的简单规则是：$ {basedir} / src / main / resources目录中放置的任何目录或文件都会在以JAR基础上开始的完全相同的结构打包进JAR内。
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
您可以看到，$ {basedir} / src / main / resources的内容可以从JAR的基础开始，我们的application.properties文件位于META-INF目录中。你还会注意到一些其他文件，比如META-INF/MANIFEST.MF以及一个pom.xml和pom.properties文件. 这些是Maven中JAR的标准生成的。你可以创建你自己的清单如果你选择的话，但是Maven会默认生成一个如果你没有创建的话（你可以修改默认清单中的实体。稍后我们会介绍）。pom.xml和pom.propertities文件被打包进JAR中，这样每个Maven产生的artifact都是可以自我描述，并且可以允许你在有需要时利用自己的应用程序的元数据。一个简单的用法可能是获取您的应用程序的版本。操作POM文件可能需要你使用一些Maven实用程序，但是使用标准的Java API利用这些属性，如下所示
```
#Generated by Maven
#Tue Oct 04 15:43:21 GMT-05:00 2005
version=1.0-SNAPSHOT
groupId=com.mycompany.app
artifactId=my-app
```
要为您的单元测试添加资源到类路径下，你遵循与向JAR添加资源相同的模式，除了放置资源的目录是$ {basedir} / src / test / resources。此时，您将有一个项目目录结构，如下所示：
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
# 如何过滤资源文件？
有时一个资源文件会需要包含一个只能在构建时提供的值。要在Maven中完成此操作，请使用语法${&lt;property name>}将包含该值的属性引用到你的资源文件中。该属性可以是您的pom.xml中定义的值之一，一个定义在用户的settings.xml中的值，一个定义在外部属性文件的值，或者一个系统属性。   
要在复制时拥有Maven过滤器资源，只需将pom.xml中的资源目录的过滤设置为true即可：
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












