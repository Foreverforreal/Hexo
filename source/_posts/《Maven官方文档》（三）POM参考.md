title: 《Maven官方文档》（三）POM参考
id: 1499068218254
author: 不识
date: 2017-07-03 15:50:20
tags:
---


# 介绍

## 什么是POM
POM表示项目对象模型（"Project Object Model）。它是在一个名为pom.xml的文件中代表Maven项目的XML。在Maven用户前，说到一个项目是在哲学的意义上说的，而不只是包含代码的文件的集合。一个项目包含配置文件，以及涉及的开发人员和它们所扮演的角色，缺陷追逐系统，组织和许可证，项目存在的URL，项目的依赖，以及其他给代码生命的小的部分。这是一个一站式的所有关于项目的事情。事实上，在Maven世界中，项目根本不需要包含任何代码，仅仅只需一个pom.xml。
<!-- more -->
## 快速概览
这是POM项目元素下的元素列表。请注意，modelVersion包含4.0.0。这是目前唯一支持Maven 2和3的POM版本，并且始终是必需的。
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <!-- 基础 -->
  <groupId>...</groupId>
  <artifactId>...</artifactId>
  <version>...</version>
  <packaging>...</packaging>
  <dependencies>...</dependencies>
  <parent>...</parent>
  <dependencyManagement>...</dependencyManagement>
  <modules>...</modules>
  <properties>...</properties>
 
  <!-- 构建设置 -->
  <build>...</build>
  <reporting>...</reporting>
 
  <!-- 更多项目信息 -->
  <name>...</name>
  <description>...</description>
  <url>...</url>
  <inceptionYear>...</inceptionYear>
  <licenses>...</licenses>
  <organization>...</organization>
  <developers>...</developers>
  <contributors>...</contributors>
 
  <!-- 环境设置 -->
  <issueManagement>...</issueManagement>
  <ciManagement>...</ciManagement>
  <mailingLists>...</mailingLists>
  <scm>...</scm>
  <prerequisites>...</prerequisites>
  <repositories>...</repositories>
  <pluginRepositories>...</pluginRepositories>
  <distributionManagement>...</distributionManagement>
  <profiles>...</profiles>
</project>
```
# 基础
POM包含关于项目的所有必要信息，以及在构建过程中要使用的插件的配置。它实际上是“谁”，“什么”和“哪里”的宣告性表现，而构建生命周期是“何时”和“如何”。这并不是说POM不会影响生命周期的流动 - 它可以。例如，通过配置maven-antrun-plugin，可以在POM中有效地嵌入ant任务。然而，最终它是一个声明。一个buid.xml当程序运行的时候它告诉ant去做什么，而一个POM说明它的配置（声明）。如果一些外部力量导致生命周期跳过ant插件执行，它将不会停止执行的插件继续做他们的工作。这个build.xml不同，它的任务几乎总是依赖于它之前执行的行。

## Maven座标
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>org.codehaus.mojo</groupId>
  <artifactId>my-project</artifactId>
  <version>1.0</version>
</project>
```
上面定义的POM是Maven 2和3将允许的最小定义。groupId：artifactId：version都是必需的字段（尽管如果从父级继承，则不需要明确定义groupId和version - 稍后会有更多关于继承）。这三个字段的行为非常类似于地址和时间戳。这在仓库中标记了一个特定的位置，就像Maven项目的座标系统一样。

- **groupId:**这个通过在一个组织或者项目之中是唯一的。例如，所有核心的Maven atrifact（应该）在groupId org.apache.maven下。在groupId不一定使用点符号，例如junit项目。注意，点标记的groupId不必与项目包含的包结构相对应。尽管这是一个很好的做法。当存储在仓库中时，该组的作用与操作系统中的Java包装结构非常相似。点被OS指定的目录分隔符（例如Unix中的“/”）替换后，它就从基本库变成了相对目录结构。在给出的示例中，org.codehaus.mojo组存在于$M2\_REPO/org/codehaus/mojo目录中。
- **artifactId:**artifactId通常是该项目被熟知的名称。尽管干groupId很重要，但是组内的人们很少会在讨论中提及groupId（它们通常都是相同的ID，例如Codehaus Mojo项目groupId：org.codehaus.mojo）它与groupId一起创建一个将该项目与世界上其他项目区分开的键（至少应该是:)）。和groupId一起，artifactId完全定义了仓库中的artifact的生存区。在上面的项目中，my-project存在于$M2\_REPO/org/codehaus/mojo/my-project。
- **version:**这是命名拼图的最后一块。groupId：artifactId表示单个项目，但它们不能描述我们正在讨论的该项目的化身。我们想要今天的junit：junit（版本4）还是四年前（版本2）？简而言之：代码更改，这些更改应该进行版本控制，并且该元素将这些版本保持一致。它也用于artifact仓库中以将版本彼此分离。my-project 1.0 版本文件存在于$M2\_REPO/org/codehaus/mojo/my-project/1.0目录结构中。
- **packaging:**现在我们有了我们的地址结构：groupId:artifactId:version，但这里还有一个更加标准的标签给我们一个真正完整的地址。那就是项目的artifact类型。在我们的例子中，上面定义的org.codehaus.mojo:my-project:1.0的示例POM将被打包成jar。我们也可以通过声明一个不同的packaging将它打包成war。
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <packaging>war</packaging>
  ...
</project>
```
当没有packaging被声明的时候，Maven假定artifact是默认值：jar。有效的类型是组件角色org.apache.maven.lifecycle.mapping.LifecycleMapping的Plexus角色提示（有关Plexus的更多信息，了解角色和角色提示的说明）。当前packaging的核心值有：pom,jar,maven-plugin,ejb,war,ear,rar,par。这些定义了针对特定包结构，执行到每个相应构建生命周期阶段的默认目标列表。    
有时你会看到Maven以groupId:artifactId:packaging:version.打印一个项目的座标。
- **classifier:**你可能在座标上偶尔发现第五个元素，那就是classifier。我们稍后会查看classifier，但现在只要知道这些项目可以显示为groupId:artifactId:packaging:classifier:version就足够了。

## POM关系
Maven的一个强大方面是它对项目关系的处理;这包含依赖（以及传递依赖），继承和聚合（多模块项目）。依赖管理有一个很长的传统，那就是对一切都复杂而混乱，但是对项目最微不足道。随着依赖树变得庞大而复杂，"Jarmageddon（Jar末日）" 很快随之而来。接着就是“Jar Hell（Jar 地狱）”，一个系统依赖的版本和开发的版本并不等同，要么是给定了错误的版本，要么类似命名的jar之间的版本冲突。Maven通过通用的本地仓库来解决这两个问题，从而正确地链接到项目，版本以及所有。
### 依赖
POM的基石是它的依赖列表。大多数项目都依赖其他项目来正确构建和运行，如果Maven所做的所有是为你管理这个列表，那么你已经得益良多。Maven在汇编或者执行其他需要它们的goal时，为你下载并链接这些依赖。作为额外的好处，Maven还引入了这些依赖的依赖（传递依赖），允许您的列表专注于项目所需的依赖项。
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.0</version>
      <type>jar</type>
      <scope>test</scope>
      <optional>true</optional>
    </dependency>
    ...
  </dependencies>
  ...
</project>
```
- **groupId, artifactId, version:**你会经常看到这些元素。这个三位一体被用于及时计算特定项目的座标，将其划分为该项目的依赖。此计算的目的是选择一个与所有依赖关系声明相匹配的版本（由于传递依赖关系，对于相同的artifact可能会有多个依赖关系声明）
- ****
- ****
- ****
- ****
- ****
- ****
- ****
- ****
#### 依赖版本要求规范
#### 排除

### 继承
#### 父POM
#### 依赖管理

### 聚合（或多模块）
#### 继承v.聚合