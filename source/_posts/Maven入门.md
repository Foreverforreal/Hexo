title: Maven入门
id: 1499308062301
author: 不识
date: 2017-07-06 10:27:46
tags:
---
# Maven概览-核心概念

Maven是一个围绕POM文件（项目对象模型）的概念。 POM文件是源代码，测试代码，依赖（使用的外部JAR）等项目资源的XML表示.POM包含对所有这些资源的引用。POM文件应位于其所属项目的根目录下。
这是一个说明Maven如何使用POM文件以及POM文件主要包含的内容的图：
![Maven核心概念](/images/other/maven-overview-1.png)
这些概念将在下面简要介绍以给你一个概览，然后在本教程后面的各自章节中有更详细的介绍。
<!-- more -->
## POM文件
当您执行Maven命令时，您可以给Maven一个POM文件来执行命令。然后，Maven将对POM中描述的资源执行命令。
## 构建生命周期，阶段和目标
Maven的构建过程分为构建生命周期，阶段和目标。一个构建生命周期包含一系列的构建阶段，每个构建阶段由一系列目标组成。当你运行Maven时，你传递一个命令到Maven。这个命令就是构建生命周期，阶段或目标的名称。如果执行一个生命周期，则这个生命周期中的所有构建阶段都会被执行。如果请求执行构建阶段，则在预定义的构建阶段序列之前的所有构建阶段都将被执行。
## 依赖和仓库
Maven首先执行的目标之一就是检查项目所需的依赖项。依赖是项目使用的外部JAR文件（Java库）。如果在Maven本地仓库库中找不到依赖项，Maven会从Maven中央仓库中下载它们，并将它们放在本地仓库中。本地仓库只是计算机硬盘上的一个目录。如果你想的话，您自己可以指定本地仓库的位置。您还可以指定要用于下载依赖远程仓库。所有这些将在本教程的后面更详细地解释。
## 构建插件
构建插件用于在构建阶段插入额外的目标。如果您需要为您的项目执行一系列不涵盖在标准Maven构建阶段和目标的操作，则可以向POM文件添加一个插件。Maven有一些可以使用的标准插件，如果需要，还可以使用Java实现自己的插件。
## 构建配置文件
如果您需要以不同的方式构建项目，则使用构建配置文件。例如，您可能需要为您的本地计算机构建项目，以进行开发和测试。并且您也可能需要构建它来部署在您的生产环境中。这两个版本可能会有所不同。要启用不同的构建，您可以向POM文件添加不同的构建配置文件。执行Maven时，您可以判断要使用的构建配置文件。

# Maven vs. Ant
Ant是Apache的另一个流行的构建工具。如果你习惯Ant，而你正在尝试学习Maven，你会注意到两个项目的方法有所不同。     
Ant使用命令式方法，这意味着您在Ant构建文件中指定Ant应该采取什么动作。您可以指定一个低级操作，如复制文件，编译代码等。您可以指定操作，还可以指定执行操作的顺序。 Ant没有默认目录布局。   
Maven使用更具声明性的方法，这意味着您在Maven POM文件中指定要构建什么，而不是如何去构建它。 POM文件描述您的项目资源 - 而不是如何构建它。相反，Ant文件描述如何构建项目。在Maven中，如何构建项目是在[Maven构建生命周期，阶段和目标](#Maven构建生命周期，阶段和目标)中预先定义的。
# Maven POM文件
Maven POM文件（项目对象模型）是描述项目资源的XML文件。这包括源代码，测试源等所在的目录，你的项目有什么外部依赖（JAR文件）等等。 

POM文件描述了要构建的内容，但最常见的不是如何构建它。如何构建它是由Maven构建阶段和目标。如果需要，您可以将自定义操作（目标）插入到Maven构建阶段。

每个项目都有一个POM文件。POM文件名为pom.xml，应位于项目的根目录中。一个项目分为几个子项目的，它通常将有一个父项目的POM文件，并且每个子项目都有一个POM文件。这种结构允许整个项目一个步构建，或者任何一个子项目要单独构建。

在本节的其余部分中，我将描述POM文件中最重要的部分。有关POM文件的完整参考，{% post_link 《Maven官方文档》（三）POM参考%}_

这是一个最小的POM文件：
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.jenkov</groupId>
    <artifactId>java-web-crawler</artifactId>
    <version>1.0.0</version>
</project>
```
modelVersion元素设置您正在使用的POM模型的版本。使用与你正在使用的Maven版本相匹配的值。版本4.0.0匹配Maven 2和3版本。

groupId元素是组织或项目（例如开源项目）的唯一ID。通常，您将使用类似于项目的根Java包名称的groupId。例如，对于我的Java Web Crawler项目，我可以选择groupId为com.jenkov。如果该项目是一个具有许多独立贡献者的开源项目，或许使用与该项目相关的groupId更有意义，而不是与我公司相关的groupId。因此，可以使用com.javawebcrawler。

groupId不必一定是Java包名称，并且不必在ID中使用.符号（点符号）来分割单词。但是，如果这样做的话，项目将位于与组ID匹配的目录结构下的Maven仓库中。每个.被替换为目录分隔符，因此每个单词都表示一个目录。这样groupId com.jenkov将位于名为MAVEN\_REPO / com / jenkov的目录中。目录名称的MAVEN\_REPO部分将替换为Maven仓库的目录路径。

artifactId元素包含您正在构建的项目的名称。在我的Java Web Crawler项目的情况下，artifactId将是java-web-crawler。artifactId用作Maven仓库中groupId目录下的子目录的名称。artifactId也用作构建项目时生成的JAR文件的名称的一部分。构建过程的输出（即构建结果）在Maven中称为artifact。大多数情况下，它是一个JAR，WAR或EAR文件，但它也可以是别的东西。 

versionId元素包含项目的版本号。如果您的项目已经以不同的版本发布，例如开放源代码API，那么版本生成是有用的。这样您的项目的用户可以参考您的项目的特定版本。版本号用作artifactId目录下的子目录的名称。版本号也用作构建的artifact名称的一部分。

上述groupId，artifactId和version元素将导致一个JAR文件被构建并放在的Maven本地仓库中，位于以下路径（目录和文件名）：
```
MAVEN_REPO/com/jenkov/java-web-crawler/1.0.0/java-web-crawler-1.0.0.jar
```

如果您的项目使用Maven目录结构，并且您的项目没有外部依赖关系，那么上述最小的POM文件就是你构建项目所需要的全部。

如果您的项目不遵循标准目录结构，具有外部依赖关系，或者在构建期间需要特殊操作，则需要向POM文件添加更多元素。这些元素列在Maven POM参考中（参见上面的链接）。

一般来说，您可以在POM中指定很多东西，为Maven提供有关如何构建项目的更多详细信息。有关可以指定的内容的更多信息，请参阅Maven POM参考。

## 超级POM 
所有Maven POM文件都继承自超级POM。如果没有指定超级POM，则POM文件将从基本POM继承。以下是图示：
![超级POM和POM继承。](/images/other/maven-super-pom.png)

您可以使POM文件明确地继承另一个POM文件。这样，您可以通过其通用的超级POM更改所有继承的POM的设置。您可以在POM文件的顶部指定超级POM，如下所示：
```
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
        <parent>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>my-parent</artifactId>
        <version>2.0</version>
        <relativePath>../my-parent</relativePath>
        </parent>
    

    <artifactId>my-project</artifactId>
    ...
</project>
```
继承的POM文件可以覆盖超级POM的设置。只需在继承的POM文件中指定新设置即可。

POM继承也在Maven POM引用中有更详细的介绍。

## 有效POM
使用所有这些POM继承，可能很难知道当Maven执行时，总体POM文件的样子。总POM文件（所有继承的结果）称为有效POM。您可以使用以下命令让Maven向您显示有效的POM：
```
mvn help:effective-pom
```
该命令将使Maven将有效的POM写入命令行提示符。

# Maven设置文件
Maven有两个设置文件。在设置文件中，您可以在所有Maven POM文件为Maven进行配置设置。例如，您可以配置：
- 本地仓库位置
- 活动构建配置文件
- 等等

设置文件名为settings.xml。两个设置文件位于：

- Maven安装目录：$M2\_HOME/conf/settings.xml 
- 用户的主目录：${user.home}/.m2/settings.xml

这两个文件都是可选的。如果两个文件都存在，则用户主文件设置文件中的值将覆盖Maven安装设置文件中的值。

您可以在{% post_link 《Maven官方文档》（二）设置参考 %} _中了解有关Maven设置文件的更多信息。

# Maven目录结构
Maven有一个标准的目录结构。如果您按照该项目的目录结构，您不需要在POM文件中指定源代码，测试代码等的目录。

您可以在[Maven标准目录布局简介](http://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html)中看到完整的目录布局。

以下是最重要的目录：

```
- src
  - main
    - java
    - resources
    - webapp
  - test
    - java
    - resources

- target
```
src目录是源代码和测试代码的根目录。main 目录是与应用程序本身相关的源代码的根目录（不是测试代码）。test 目录包含测试源代码。 main和test下的java目录包含应用程序本身的Java代码（主要内容）和测试的Java代码（被测试）。  
资源目录包含项目所需的其他资源。这可以是用于应用程序国际化的属性文件，或其他内容。

如果您的项目是Web应用程序，则webapp目录包含您的Java Web应用程序。webapp目录将是Web应用程序的根目录。因此，webapp目录包含WEB-INF目录等

target目录由Maven创建。它包含由Maven生成的所有编译的类，JAR文件等。执行cleang构建阶段时，将清除目标目录。

# 依赖

## 外部依赖
Maven中的外部依赖是一个不在Maven仓库（既不在本地仓库，也不在中央仓库，远程仓库）中的依赖（JAR文件）。它可能位于本地硬盘上的某个位置，例如位于webapp的lib目录或其他位置。因此，“外部”一词意味着在Maven仓库系统的外部 - 而不仅仅是项目外部。大多数依赖都在在项目外部，但在仓库系统外部（不在仓库中）的很少。

你可以在像这样配置外部依赖
```
<dependency>
  <groupId>mydependency</groupId>
  <artifactId>mydependency</artifactId>
  <scope>system</scope>
  <version>1.0</version>
  <systemPath>${basedir}\war\WEB-INF\lib\mydependency.jar</systemPath>
</dependency>
```
groupId和artifactId都设置为依赖的名称。也就是使用的API的名称。scope 元素值设置为system。 systemPath元素设置为指向包含依赖关系的JAR文件的位置。$ {basedir}指向POM所在的目录。该路径的其余部分是是相对目录的位置。

## 快照依赖

快照依赖关系是正处在开发阶段的依赖（JAR文件）。相比经常更新版本号来获取最新的版本，你可以依赖于项目的快照版本。快照版本会为每次构建下载到你的本地仓库中，即使在你的本地仓库中一个匹配的快照版本已经存在。始终下载快照依赖确保您在本地仓库中始终拥有最新版本，以用于每次构建。   
你可以通过将-SNAPSHOT附加到POM下的version元素（您也可以设置groupId和artifactId的地方），告诉Maven您的项目是一个快照版本。这是一个version元素示例：
```xml
<project>
<version>1.0-SNAPSHOT</version>
</project>
```
注意版本号后附加的-SNAPSHOT。

你也可以通过在配置依赖关系后在版本号之后附加-SNAPSHOT来完成设置依赖一个快照版本。下面是示例
```xml
<dependency>
    <groupId>com.jenkov</groupId>
    <artifactId>java-web-crawler</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```
版本号附加的-SNAPSHOT告诉Maven这是一个快照版本。

您可以配置Maven在Maven设置文件中下载快照依赖关系的频率。

# Maven仓库
Maven仓库是具有额外元数据的打包JAR文件的目录。元数据是描述JAR文件所属项目的POM文件，包括每个打包JAR的外部依赖关系。正是这个元数据使Maven能够递归地下载依赖的依赖，直到整个依赖关系树下载并放入本地存储库中。

Maven仓库在Maven[“仓库简介”](http://maven.apache.org/guides/introduction/introduction-to-repositories.html)中有更详细的介绍，但这里是一个简要的概述。

Maven有三种类型的仓库：
- 本地仓库
- 中央仓库
- 远程仓库

Maven在上述顺序中搜索这些仓库中的依赖关系。首先在本地仓库中搜索，然后在中央仓库中，如果在POM中有指定，最后在远程仓库中。

![Maven仓库类型和位置](/images/other/maven-repositories.png)

## 本地仓库
本地仓库是开发人员计算机上的目录。该仓库将包含Maven下载的所有依赖项。相同的Maven存储库通常用于多个不同的项目。因此，Maven只需要下载依赖关系，即使多个项目依赖于它们（例如Junit）。

您也可以使用mvn install命令，在本地仓库中构建并安装自己的项目。这样，您的其他项目可以将您自己的项目的打包JAR文件作为外部依赖，将它们指定为Maven POM文件中的外部依赖项。

默认情况下，Maven将本地存储库放在本地计算机上的用户主目录中。但是，您可以通过设置Maven设置文件中的目录来更改本地仓库的位置。您的Maven设置文件也位于user-home/.m2目录中，文件名为settings.xml。以下是为本地仓库指定其他位置的方法：
```xml
<settings>
    <localRepository>
        d:\data\java\products\maven\repository
    </localRepository>
</settings>
```
## 中央仓库

Maven中央仓库是由Maven社区提供的一个存储库。默认情况下，如果本地仓库中找不到，Maven在此中央仓库中查找所需的任何依赖。 Maven然后将这些依赖下载到您的本地仓库。您无需特殊的配置来访问中央仓库。

## 远程仓库
远程仓库是Web服务器上的仓库，Maven可以从其中下载依赖，就像中央仓库一样。远程仓库可以位于互联网上的任何地方，也可以位于本地网络内。

远程仓库通常用于托管您组织内部的项目，这些项目由多个项目共享。例如，可以在多个内部项目中使用通用的安全项目。这个安全项目不应该被外部世界访问，因此不应该被托管在公共的Maven中央仓库中。相反，它可以托管在内部远程仓库。  

在远程仓库中找到的依赖也由Maven下载并放入本地仓库。

您可以在POM文件中配置远程仓库。将以下XML元素放在&lt;dependencies>元素之后：
```xml
<repositories>
   <repository>
       <id>jenkov.code</id>
       <url>http://maven.jenkov.com/maven2/lib</url>
   </repository>
</repositories>
```
# Maven构建生命周期，阶段和目标

当Maven构建一个软件项目时，它遵循构建生命周期。构建生命周期分为构建阶段，构建阶段分为构建目标。Maven构建生命周期，构建阶段和目标在{% post_link 《《Maven官方文档》（二）构建生命周期介绍 %} _中有更详细的描述，但在这里我将给您一个快速的概述。

## 构建生命周期

Maven有三个内置生命周期，它们是
1. **default**
2. **clean**
3. **site**

这些构建生命周期中的每一个都处理构建软件项目的不同方面。因此，这些构建生命周期中的每一个彼此独立地执行。您可以让Maven执行多个构建生命周期，但它们将按顺序执行，彼此分开，就好像已经执行了两个单独的Maven命令一样。
default生命周期处理与编译和打包项目相关的所有内容。clean的生命周期处理与从输出目录中删除临时文件相关的所有内容，包括生成的源文件，编译的类，以前的JAR文件等。site生命周期处理与为您的项目生成文档相关的所有内容。事实上，site可以生成一个完整的网站，其中包含您项目的文档。

## 构建阶段
每个构建生命周期分为一系列构建阶段，构建阶段再次细分为目标。因此，总体构建过程是一系列的构建生命周期，构建阶段和目标。

您可以执行整个构建生命周期如clean或site，或者一个构建阶段如install，这是default构建生命周期的一部分，或一个构建目标，如 dependency:copy-dependencies。注意：您不能直接执行default生命周期。您必须在default生命周期内指定构建阶段或目标。

当你执行一个构建阶段，在这个标准构建阶段序列中所有之前的构建阶段都会被执行。因此，执行install构建阶段实际意味着执行在install阶段之前所有构建阶段，然后再执行install阶段。

default的生命周期是最有意思的，因为它建立代码。由于您无法直接执行default生命周期，因此您需要执行deafault生命周期中的构建阶段或目标。

deafault生命周期有一个广泛的构建阶段和目标序列，但这里不描述。最常用的构建阶段是：

|构建阶段|描述|
|--------|----|
|validate|验证项目是否正确，是否提供了所有必要的信息。这也确保了依赖被下载。|
|compile|编译项目源代码|
|test|使用合适的单元测试框架测试编译的源代码。这些测试不应该要求代码被打包或部署|
|package|编译代码并将其打包成可分发格式，如JAR。|
|install|将软件包安装到本地仓库中，以作为本地其他项目的依赖使用|
|deploy	|将最终软件包复制到远程仓库，以与其他开发人员和项目共享。|

通过将其名称传递给mvn命令来执行其中一个构建阶段。这是一个例子：
```
mvn package
```
此示例执行package构建阶段，也执行在此阶段在Maven预定义构建阶段序列之前所有的阶段。


如果标准的Maven构建阶段和目标不足以构建项目，则可以创建Maven插件以添加所需的额外构建功能。

## 构建目标
构建目标是Maven构建过程中最精细的步骤。一个目标可以绑定到一个或多个构建阶段，或者根本没有。如果目标没有绑定到任何构建阶段，则只能通过将目标名称传递给mvn命令来执行。如果一个目标被绑定到多个构建阶段，那么该目标将在每个构建阶段被执行。


# Maven构建配置

Maven构建配置使您能够使用不同的配置构建项目。相比创建两个单独的POM文件，您可以只需指定一个带有不同构建配置的profile，并在需要时使用此构建配置文件构建项目。

您可以在[“Profile”](http://maven.apache.org/pom.html#Profiles)下的Maven POM参考资料中阅读有关构建配置文件的完整故事。这里我会给你一个快速的概述。

Maven配置文件指定在POM文件中，在profiles元素内。每个构建配置文件嵌套在profile元素中。这有一个例子：
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
   http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.jenkov.crawler</groupId>
  <artifactId>java-web-crawler</artifactId>
  <version>1.0.0</version>

  <profiles>
      <profile>
          <id>test</id>
          <activation>...</activation>
          <build>...</build>
          <modules>...</modules>
          <repositories>...</repositories>
          <pluginRepositories>...</pluginRepositories>
          <dependencies>...</dependencies>
          <reporting>...</reporting>
          <dependencyManagement>...</dependencyManagement>
          <distributionManagement>...</distributionManagement>
      </profile>
  </profiles>

</project>
```
构建配置文件描述在该构建配置文件下执行时应对POM文件进行哪些更改。这可能会更改要使用的应用程序配置文件等。配置文件元素中的元素将覆盖POM中具有相同名称的元素的值。

在profile元素内可以看到activation元素。该元素描述触发此构建配置文件被使用的条件。选择执行那个profile的一个方式在setting.xml文件中。这里你可以设置激活的profile。另一种方法是将-P profile-name添加到Maven命令行。有关详细信息，请参阅配置文件文档。

# Maven插件
Maven插件使您能够将自己的操作添加到构建过程中。您可以通过创建一个简单的Java类来继承一个特殊的Maven类，然后为该项目创建一个POM。该插件应该位于其自己的项目中。

# Maven命令
Maven包含一系列你可以执行的命令。Maven命令是构建生命周期，构建阶段和构建目标的混合，因此可能会有点混乱。因此，我将在本教程中描述常见的Maven命令，并解释其正在执行的构建生命周期，构建阶段和构建目标。

## Maven命令结构
一个Maven命令包含这两个元素
- mvn
- 一个或多个构建周期，构建阶段或构建目标

这是一个Maven命令示例：
```
mvn clean
```
该命令由执行Maven的mvn命令和名为clean的构建生命周期组成。   
这是另一个Maven命令示例：
```
mvn clean install
```
这个maven命令执行clean构建生命周期和default生命周期中的install构建阶段。

您可能会想知道如何看待构建生命周期，构建阶段和构建目标之间的区别。稍后会说明。

## 构建生命周期，阶段和目标

如关于构建生命周期，构建阶段和构建目标的部分中的介绍中提到的，Maven包含三个主要的构建生命周期：
- clean
- default
- site

在每个构建生命周期内都有构建阶段，每个构建阶段都有构建目标。      

您可以执行构建生命周期，构建阶段或构建目标。当执行构建生命周期时，执行了构建生命周期内执行所有构建阶段（以及构建目标）。

执行构建阶段时，会执行构建阶段内执行所有的构建目标。 Maven还执行了构建生命周期中的希望的构建阶段之前所有的构建阶段。

Buid目标被分配到一个或多个buid阶段。执行构建阶段时，构建阶段的所有目标也是如此。您也可以直接执行构建目标。

## 执行生命周期，阶段和目标

运行mvn命令时，可以传递一个或多个参数。这些参数指定构建生命周期，构建阶段或构建目标。例如，要执行clean构建生命周期，请执行以下命令：
```
mvn clean
```
要执行站点构建生命周期，请执行以下命令：
```
mvn site
```
# 执行Default生命周期
default的生命周期是生成，编译，打包等你的源代码的构建生命周期。

您无法像clean或site一样直接执行default构建生命周期。相反，您必须在default构建生命周期内执行特定的构建阶段。
# 执行构建阶段 
通过将构建阶段的名称传递给Maven命令，可以在构建生命周期内执行构建阶段。这里有几个构建阶段命令示例：
```
mvn pre-clean

mvn compile
    
mvn package
```
Maven会查找指定的构建阶段所属的构建生命周期，因此您不需要明确指定构建阶段所属的构建生命周期。

# Maven原型
Maven原型是可以为您的Maven生成的项目模板。换句话说，当您开始一个新项目时，您可以使用Maven为该项目生成一个模板。在Maven中，一个模板被称为原型（archetype）。因此，每个Maven原型对应于Maven可以生成的项目模板。

## 可用的Maven原型
Maven包含很多原型，所以这个Maven原型教程将只显示一些最常用的原型。要查看Maven原型的完整列表，只需运行以下命令即可：
```
mvn archetype:generate
```
该命令实际上是为您生成一个Maven原型，但是由于您没有在命令中指定要构建的原型，Maven将会将其所有可用的原型输出到命令提示符。最后，Maven会询问你生成哪一个Maven原型。如果您知道要生成的原型的编号，则可以在命令提示符中键入数字，然后按Enter键。

该列表包含超过1.300个Maven原型，因此找到所需的原型并不是那么容易。看看可用的Maven原型列表，您可以将输出管道输入到一个文件中，然后打开该文件。Notepad++ 等等。您可以使用此Maven命令将可用的Maven原型管理到文件中：
```
mvn archetype:generate > archetypes.txt
```

您可能需要在要求您输入原型号码的地方取消该命令。您可以使用CTRL-C在Windows上执行此操作。原型仍将写入文件。

## 命名原型
Maven包含一组您可以创建的命名原型。我将在以下部分中列出其中的几个原型。
### Eclipse
有一个Maven原型可以生成一个新的Java项目，包括Eclipse IDE的文件。您可以使用此Maven命令生成该原型：
```
mvn eclipse:eclipse
```
在生成此Maven原型之前，您需要在要生成原型的项目根目录中有一个POM文件。
### IDEA原型
与Eclipse原型类似，Maven包含一个IntelliJ IDEA原型。您可以使用此Maven命令生成IDEA原型：
```
mvn idea:idea
```
# Maven单元测试报告
Maven可以以HTML形式生成单元测试报告。 Maven通过运行单元测试并记录单元测试的结果来做到这一点。然后Maven从单元测试结果生成一个HTML报告。

使用Maven生成单元测试报告可以在构建过程中看到哪些单元测试失败。特别是如果构建很大并且需要很长时间，那么因为在开发过程中您可能不会在IDE内部运行所有的单元测试。您只能运行与您所做更改相关的单元测试。然后，要检查项目中的所有内容是否仍然有效，您可以告诉Maven运行单元测试并生成单元测试报告。然后，您可以阅读报告，查看在构建期间是否有任何单元测试失败。

Maven单元测试报告是一个HTML文件。这意味着如果您需要与其他开发人员共享，则可将其复制到服务器。

## Maven Surefire插件
Maven单元测试报告由Maven Surefire插件生成。因此，单位测试报告也有时被称为Surefire 报告。

## 生成一个单元测试报告
您使用以下Maven命令生成一个Maven的单元测试报告（Surefire单元测试报告）：
```
mvn surefire-report:report
```
生成的单元测试报告可以在target/site目录中找到。单元测试报告名为surefire-report.html。因此，单元测试报告的路径是：
```
your-project/target/site/surefire-report.html
```

## 跳过测试
有时您可能希望Maven生成单元测试报告，而不再重新运行所有单元测试。
您可能只想使用上次运行的结果。你可以使用以下命令让Maven生成单元测试报告，而无需重新运行单元测试：
```
mvn surefire-report:report-only
```
此命令仅生成单元测试报告。