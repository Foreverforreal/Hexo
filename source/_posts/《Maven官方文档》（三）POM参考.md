title: 《Maven官方文档》（三）POM参考
id: 1499068218254
author: 不识
tags:
  - Maven
categories:
  - Maven
  - 参考
date: 2017-07-03 15:50:00
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
 
  <!-- 基本设置 -->
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
# 基本设置
POM包含关于项目的所有必要信息，以及在构建过程中要使用的插件的配置。它实际上是“谁”，“什么”和“哪里”的宣告性表现，而构建生命周期是“何时”和“如何”。这并不是说POM不会影响生命周期的流动 - 它可以。例如，通过配置maven-antrun-plugin，可以在POM中有效地嵌入ant任务。然而，最终它是一个声明。一个buid.xml当程序运行的时候它告诉ant去做什么，而一个POM说明它的配置（声明）。如果一些外部力量导致生命周期跳过ant插件执行，它将不会停止执行的插件继续做他们的工作。这个build.xml不同，它的任务几乎总是依赖于它之前执行的行。

## Maven坐标
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
上面定义的POM是Maven 2和3将允许的最小定义。groupId：artifactId：version都是必需的字段（尽管如果从父级继承，则不需要明确定义groupId和version - 稍后会有更多关于继承）。这三个字段的行为非常类似于地址和时间戳。这在仓库中标记了一个特定的位置，就像Maven项目的坐标系统一样。

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
有时你会看到Maven以groupId:artifactId:packaging:version.打印一个项目的坐标。
- **classifier:**你可能在坐标上偶尔发现第五个元素，那就是classifier。我们稍后会查看classifier，但现在只要知道这些项目可以显示为groupId:artifactId:packaging:classifier:version就足够了。

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
- **groupId, artifactId, version:**你会经常看到这些元素。这个三位一体被用于及时计算特定项目的坐标，将其划分为该项目的依赖。此计算的目的是选择一个与所有依赖关系声明相匹配的版本（由于传递依赖关系，对于相同的artifact可能会有多个依赖关系声明）
	- **groupId, artifactId: **依赖的直接对应坐标。
	- **version: **一个依赖的版本说明规范，这将用来计算依赖的有效版本。
  
自从依赖由Maven坐标来描述，你可能会想：“这意味着我的项目只能依靠Maven artifact”，而答案是“当然，但这是一件好时期”。这迫使您完全依靠于Maven可以管理的依赖关系。有时候，不幸的是，当一个项目无法从Maven中央库中下载时。例如，一个项目可能依赖于具有封闭源许可证的jar，该jar禁止它在中央存储库中。处理这种情况有三种方法。
1. 使用安装插件本地安装依赖。该方法是最简单的推荐方法。比如
```
mvn install:install-file -Dfile=non-maven-proj.jar -DgroupId=some.group -DartifactId=non-maven-proj -Dversion=1 -Dpackaging=jar
```
注意这里依旧需要地址，只有这时使用命令行和安装插件会为你给定的地址创建一个POM。
2. 创建自己的仓库并将其部署在那里。这是对于拥有内网并需要使每个人都能同步的公司最受欢迎的方法。这有一个Maven目标称作deploy:deploy-file，它与install:install-file目标相似（阅读插件的目标页面了解更多信息）。
3. 设置依赖雨为system，并且定义一个systemPath。然而，并不建议这么做，但是它使我们要解释以下元素。
	- **classifier:**classifier允许我们区分从同一个POM构建但是内容不同的artifact。它是一些可选的和任意的字符串， - 如果存在的话 - 附加到artifact名称后，就是在版本号之后。     
		此元素的用意是，考虑这样一个例子，一个项目提供一个针对JRE 1.5的artifact，但是同时也依然支持JRE 1.4。这样第一个artifact可以配备classifier jdk15，而第二个是jdk14，以便客户端选择一个使用。    
		classifier的另一个常见用例是将辅助artifact附加到项目的主artifact上。如果你浏览Maven的中央仓库，你会注意到classifier source和javadoc经常和打包类文件一起用于部署项目源代码和API文档。   
	- **type:**
    对应于依赖artifact的packaging类型。默认值是jar。它通常表示依赖文件名的拓展名，但并不都是这样。一个可以映射到不同的拓展名和classifier。type通常对应于packaging所使用的，但也并非总是如此。一些例子比如jar，ejb-client和test-jar。新的类型可以由插件设置extension为true来定义，因此这不是完整的列表。
	- **scope:**
    该元素指的是当前任务的类路径（编译和运行，测试等）以及如何限制依赖关系的传递性。这又五个scope可用。      
		- **compile - **这是默认的scope，如果没有指定的话，默认使用它。编译范围依赖在所有classpath中都可用。此外，这些依赖关系被传播到依赖项目。
		- **provided -**这很像compile，但是表示您期望JDK或容器在运行时提供它。它只适用于编译和测试类路径，不可传递。
		- **runtime - **这个scope表示编译时不需要该依赖，但是执行时需要。它是在运行时和测试类路径，而不是编译类路径。
		- **test -**这个scope表示应用程序正常使用的时候并不需要该依赖，只是适用于测试的编译和执行阶段。它不可传递。
		- **system -**这个scope类似与provided只是你必须提供明确包含它的JAR。该artifact始终可用，但不是在存储库中查找。
	- **systemPath:**只有在依赖scope是system时使用。否则的话，如果设置这个元素构建会失败。这个路径必须是绝对路径，因此建议使用属性来指定特定于机器的路径（关于属性以下更多），比如${java.home}/lib。由于假设先前安装了syste范围的依赖，Maven将不会为项目检查仓库，而是检查以确保这个文件存在。如果不存在的话，Maven会构建失败并且建议你手工下载安装它。
	- **optional:**当项目本身是一个依赖的时候，标记项目的依赖为optional。困惑？比如，假设一个项目A依赖项目B来编译可能在运行时不使用的的一部分代码，则我们可能对于整个项目来说不需要项目B。所以如果项目X添加项目A作为它的依赖，那么Maven根本就不需要安装项目B。符号上，如果 =>表示所需的依赖关系，而 - >表示可选的，尽管A => B是在构建A时的情况，但是构建X时则是X=>A-->B 。
    简而言之，optional让其他项目知道，当你使用这个项目，你不需要该依赖也能正确运行。


#### 依赖版本要求规范
依赖的version元素定义了版本说明，用来极速有效的依赖版本。版本要求具有以下语法：
- 1.0: “软”要求1.0（只是一个建议，如果它匹配所有其他范围的依赖）
- [1.0]：1.0的“硬”要求
- (，1.0]：x <= 1.0
- [1.2,1.3]：1.2 <= x <= 1.3
- [1.0,2.0]：1.0 <= x <2.0
- [1.5，)：x> = 1.5
- (，1.0]，[1.2，)：x <= 1.0或x> = 1.2;多个集合以逗号分隔
- (，1.1)，(1.1,)：排除1.1（例如，如果知道不与此库结合使用）

#### Exclusions
Exclusions明确的告诉Mave，你不想包含这个指定的项目是这个依赖的一个依赖（换句话说，它的传递依赖）。比如，maven-embedder需要maven-core，但是我们不希望使用它或它的依赖，这样我们就可以添加它作为一个exclution。
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <dependencies>
    <dependency>
      <groupId>org.apache.maven</groupId>
      <artifactId>maven-embedder</artifactId>
      <version>2.0</version>
      <exclusions>
        <exclusion>
          <groupId>org.apache.maven</groupId>
          <artifactId>maven-core</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
    ...
  </dependencies>
  ...
</project>
```
有时用来剪切一个依赖的传递依赖也是很有用的。一个依赖可能不正确的指定scope，或者依赖可能与你项目中的其他依赖相冲突。使用通配符来排除可以轻松的排除一个依赖的所有传递依赖。在下面的情况中，您可能正在使用maven-embedder，并且你希望使用自己的来管理依赖项，所以你剪切掉了所有的传递依赖：
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <dependencies>
    <dependency>
      <groupId>org.apache.maven</groupId>
      <artifactId>maven-embedder</artifactId>
      <version>3.1.0</version>
      <exclusions>
        <exclusion>
          <groupId>*</groupId>
          <artifactId>*</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
    ...
  </dependencies>
  ...
</project>
```
- **exclutions:**Exclusions包含一个或多个exclution元素，每一个都包含一个groupId和artifactId表示该依赖被排除。不像optional，它表示可能被安装使用，也可能没有被安装使用，exclutions主动将它们自己从依赖树中删除。


### 继承
Maven带来的一个强大的附加功能就是项目继承的概念。虽然在构建系统比如Ant，继承确实可以模拟继承，但是Maven更进一步的明确了项目对象模型的继承关系。
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>org.codehaus.mojo</groupId>
  <artifactId>my-parent</artifactId>
  <version>2.0</version>
  <packaging>pom</packaging>
</project>
```
对于parent和aggregation（多模块）项目packaging被要求使用pom。这些类型定义了绑定到一组生命周期阶段的目标。例如，如果packaging 是jar，那么package时期将执行jar：jar目标。如果packagin是pom，执行的目标会是site:attach-descriptor。现在我们可以在父POM中添加值，该POM将由其子类继承。父POM中的大多数元素都由其子项继承，包括：
- groupId
- version
- description
- url
- inceptionYear
- organization
- licenses
- developers
- contributors
- mailingLists
- scm
- issueManagement
- ciManagement
- properties
- dependencyManagement
- dependencies
- repositories
- pluginRepositories
- build
	- plugin executions with matching ids
	- plugin configuration
	- etc.
- reporting
- profiles

不被继承的值得注意的元素由
- aritfactId
- name
- prerequisites

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <parent>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>my-parent</artifactId>
    <version>2.0</version>
    <relativePath>../my-parent</relativePath>
  </parent>
 
  <artifactId>my-project</artifactId>
</project>
```
注意relativePath元素。它不是必需的，但可以用作Maven的指示符用来首先搜索给定该项目父级的路径，然后再搜索本地和远程存储库。   
要想实际查看继承，可以看一下[ASF](https://svn.apache.org/viewvc/maven/pom/trunk/asf/pom.xml?view=markup)和[Maven](https://svn.apache.org/viewvc/maven/pom/trunk/maven/pom.xml?view=markup)的父POM。
#### 超级POM
类似于面向对象编程中对象的继承，POM拓展自一个父POM，并从它的父级继承某些值。而且，就像Java对象最终从java.lang.Object继承而来，所有项目对象模型都从基础超级POM继承而来。下面的代码片段是Maven 3.0.4的超级POM。
```xml
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <repositories>
    <repository>
      <id>central</id>
      <name>Central Repository</name>
      <url>http://repo.maven.apache.org/maven2</url>
      <layout>default</layout>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
    </repository>
  </repositories>
 
  <pluginRepositories>
    <pluginRepository>
      <id>central</id>
      <name>Central Repository</name>
      <url>http://repo.maven.apache.org/maven2</url>
      <layout>default</layout>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
      <releases>
        <updatePolicy>never</updatePolicy>
      </releases>
    </pluginRepository>
  </pluginRepositories>
 
  <build>
    <directory>${project.basedir}/target</directory>
    <outputDirectory>${project.build.directory}/classes</outputDirectory>
    <finalName>${project.artifactId}-${project.version}</finalName>
    <testOutputDirectory>${project.build.directory}/test-classes</testOutputDirectory>
    <sourceDirectory>${project.basedir}/src/main/java</sourceDirectory>
    <scriptSourceDirectory>src/main/scripts</scriptSourceDirectory>
    <testSourceDirectory>${project.basedir}/src/test/java</testSourceDirectory>
    <resources>
      <resource>
        <directory>${project.basedir}/src/main/resources</directory>
      </resource>
    </resources>
    <testResources>
      <testResource>
        <directory>${project.basedir}/src/test/resources</directory>
      </testResource>
    </testResources>
    <pluginManagement>
      <!-- NOTE: These plugins will be removed from future versions of the super POM -->
      <!-- They are kept for the moment as they are very unlikely to conflict with lifecycle mappings (MNG-4453) -->
      <plugins>
        <plugin>
          <artifactId>maven-antrun-plugin</artifactId>
          <version>1.3</version>
        </plugin>
        <plugin>
          <artifactId>maven-assembly-plugin</artifactId>
          <version>2.2-beta-5</version>
        </plugin>
        <plugin>
          <artifactId>maven-dependency-plugin</artifactId>
          <version>2.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-release-plugin</artifactId>
          <version>2.0</version>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
 
  <reporting>
    <outputDirectory>${project.build.directory}/site</outputDirectory>
  </reporting>
 
  <profiles>
    <!-- NOTE: The release profile will be removed from future versions of the super POM -->
    <profile>
      <id>release-profile</id>
 
      <activation>
        <property>
          <name>performRelease</name>
          <value>true</value>
        </property>
      </activation>
 
      <build>
        <plugins>
          <plugin>
            <inherited>true</inherited>
            <artifactId>maven-source-plugin</artifactId>
            <executions>
              <execution>
                <id>attach-sources</id>
                <goals>
                  <goal>jar</goal>
                </goals>
              </execution>
            </executions>
          </plugin>
          <plugin>
            <inherited>true</inherited>
            <artifactId>maven-javadoc-plugin</artifactId>
            <executions>
              <execution>
                <id>attach-javadocs</id>
                <goals>
                  <goal>jar</goal>
                </goals>
              </execution>
            </executions>
          </plugin>
          <plugin>
            <inherited>true</inherited>
            <artifactId>maven-deploy-plugin</artifactId>
            <configuration>
              <updateReleaseInfo>true</updateReleaseInfo>
            </configuration>
          </plugin>
        </plugins>
      </build>
    </profile>
  </profiles>
 
</project>
```
您可以通过创建一个最小的pom.xml并在命令行中执行：mvn help:effective-pom来查看超级POM如何影响你的项目对象模型。
#### 依赖管理
除了继承某些顶级元素之外，父级有一个元素可以为子级POM或传递依赖配置值。其中一个元素就是dependencyManagement。
- **dependencyManagement:**是POM用来帮助管理所有子项的依赖信息。如果my-parent项目使用dependencyManagement来定义对junit：junit：4.0的依赖关系，那么继承自它的POM可以只给定groupId=junit和artifactId=junit来设置依赖。这种方法的好处很明显。依赖关系细节可以设置在一个中心位置，这将传播到所有继承的POM。
	注意，从传递依赖并入的artifact的version和scope在依赖管理部分同样由版本说明控制。这可能会导致意想不到的后果。考虑这样一个情况，你的项目中有两个依赖，dep1和dep2。dep2也使用dep1，并且需要一个特定最低版本才能运行。然后如果使用dependencyManagement指定一个较旧的版本，dep2将被迫使用旧版本，并且失败。所以，你必须小心检查整个依赖关系树来避免这个问题; mvn dependency:tree会有帮助的。
    
### 聚合（或多模块）
具有模块的项目被称为多模块或聚合项目。模块是该POM列出并作为一组执行的项目。一个pom打包的项目可以通过将它们列为模块来聚合一组项目的构建，它们是这些项目的相对目录。
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>org.codehaus.mojo</groupId>
  <artifactId>my-parent</artifactId>
  <version>2.0</version>
  <packaging>pom</packaging>
 
  <modules>
    <module>my-project</module>
    <module>another-project</module>
  </modules>
</project>
```

您不需要自己在列出模块时考虑模块之间的依赖关系，即POM给出的模块的排序并不重要。Maven将拓扑地对模块进行排序，使得依赖模块始终在需要依赖模块之前构建。

要想实际查看聚合，可以看一下[Maven](https://svn.apache.org/viewvc/maven/maven-3/trunk/pom.xml?view=markup)和[Maven Core Plugins](https://svn.apache.org/viewvc/maven/plugins/trunk/pom.xml?view=markup)的父POM。
#### 继承v.聚合
继承和依赖通过单个高级的POM创建了一个很好的动态构建控制。你会经常同时是父级和聚合的项目。比如，整个maven核心通过一个基本的POM org.apache.maven:maven运行，所以构建Maven项目可以通过单个命令：mvn compile来执行。然和，尽管POM项目，聚合项目和父项目都不是同一个项目，也不应该被混淆。一个POM项目可以被它聚合的任何模块所继承，但这不是必须的。相反，一个POM项目可能会聚合一个不继承它的项目。
## 属性
属性是了解POM基础知识的最后一个要素。Maven属性是值占位符，就像Ant中的属性一样。在POM中它们的值可以通过符号${X}在任何地方访问，这里X是属性。或者它们可以被插件用作默认值，比如
```xml
<project>
  ...
  <properties>
    <maven.compiler.source>1.7<maven.compiler.source>
    <maven.compiler.target>1.7<maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
  </properties>
  ...
</project>
```
他们有五种不同的风格：

1. env.X:在变量前加上前缀“env.”。会返回shell的环境变量。比如${env.PATH}包含PATH环境变量。
	注意：虽然环境变量本身在Windows上不区分大小写，但查找属性区分大小写。换而言之，虽然Windows shell为%PATH%和%path%返回相同的值，但Maven还是会区分${env.PATH}和${env.Path}。对于Maven 2.1.0，为了可靠性，环境变量的名称被标准化为全部大写。
2. project.x:在POM中一个点（.）标记的路径会包含对应的元素的值。比如&lt;project> &lt;version> 1.0 </ version> </ project>可以通过$ {project.version}访问。
3. setting.x:在setting.xml中一个点（.）标记的路径会包含对应的元素值。比如：&lt;settings> &lt;offline> false </ offline> </ settings>可通过$ {settings.offline}访问。
4. Java系统属性：所有的可以通过java.lang.System.getProperties()访问的属性都可以用作POM属性，比如${java.home}。
5. x:在POM中的<properties />元素中设置。&lt;properties> &lt;someVar>value</ someVar> </ properties>可以当作$ {someVar}使用。

# 构建设置
除了上述POM的基础知识之外，还有两个元素在声明POM的基本能力之前必须要理解。它们是处理像声明你的项目目录结构并且管理插件这些事情的build元素；以及reporting元素，它为报告目的最大的反应构建元素。
## 构建
构建POM 4.0.0 XSD，build元素在概念上被分为两个部分：一个BaseBuild类型，它包含两个build元素共有的元素组（project下的顶级build元素和profile下的build元素，如下所示）；还有一个Build类型，它包含BaseBuild集合以及顶层定义的更多元素。我们首先分析两者之间的共同点。

***注意：这些不同的build元素可以被表示为“项目构建”和“配置文件构建”。***
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <!-- "Project Build"包含比仅仅BaseBuild集更多的元素 -->
  <build>...</build>
 
  <profiles>
    <profile>
      <!-- "Profile Build"包含“Project Build”元素的子集 -->
      <build>...</build>
    </profile>
  </profiles>
</project>
```
### 基础构建元素
BaseBuild完全像它听到的一样：是存在于POM中两种build元素的基础元素集。
```xml
<build>
  <defaultGoal>install</defaultGoal>
  <directory>${basedir}/target</directory>
  <finalName>${artifactId}-${version}</finalName>
  <filters>
    <filter>filters/filter1.properties</filter>
  </filters>
  ...
</build>
```
- **defaultGoal：**如果没有给出，则执行的默认目标或阶段。如果给出了一个目标，它应该被定义为它在命令行中（如jar:jar）。如果定义了一个阶段（如install），也是如此。
- **directory：**这是构建会将它产生的文件转储的目录，或者以Maven的说法来说，构建的target。它恰好默认为${basedir}/target。
- **finalName：**这是捆绑项目最终构建时的名称（没有文件扩展名，例如：my-project-1.0.jar）。它默认为$ {artifactId}-$ {version}。但是，“finalName”有些用词不当，构建捆绑项目的插件有权忽略/修改此名称（但通常不会）。比如，如果maven-jar-plugin被配置为给一个jar一个test的classifier，那么上面定义的实际jar将被构建为my-project-1.0-test.jar。
- **filter：**定义\* .properties文件，其中包含适用于接受其设置的资源的属性列表（如下所述）。换句话说，filter文件中定义的“name=value”对在构建资源时替换${name}字符串。上面的示例定义了filter/directory下的filter1.properties文件。Maven的默认filter目录是${basedir}/src/main/filters/。    

	要更全面地了解filter以及可以做什么，请查看{% post_link 《Maven官方文档》（一）入门指南 %}。_
   
#### Resources
build元素的另一个特征是指定项目中存在资源的位置。资源（通常）不是代码。它们不会被编译，而是意在与你的项目捆绑在一起的条目，或者用于各种其他原因，如代码生成。
比如，一个Plexus项目需要在META-INF/plexus目录下的configuration.xml文件（它指定容器的组件配置）。虽然我们可以很容易地将这个文件放在src/main/ resource/META-INF/plexus中，但是我先希望给Plexus一个它自己的目录src/main/plexus。为了使JAR插件正确地捆绑资源，你应该类似于下面内容指定资源
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <build>
    ...
    <resources>
      <resource>
        <targetPath>META-INF/plexus</targetPath>
        <filtering>false</filtering>
        <directory>${basedir}/src/main/plexus</directory>
        <includes>
          <include>configuration.xml</include>
        </includes>
        <excludes>
          <exclude>**/*.properties</exclude>
        </excludes>
      </resource>
    </resources>
    <testResources>
      ...
    </testResources>
    ...
  </build>
</project>
```

- **resources：**是一个resource元素列表，每个resource元素都描述与此项目相关联的有哪些文件，以及这些文件的位置。
- **targetPath：**指定放置从构建生成的资源集的目录结构。目标路径默认为基本目录。一个会被打包在JAR中资源通用指定目标路径是META-INF。
- **filtering：**是它true或false，表示是否要为此资源启用过滤。注意，该过滤\*.properties文件不必。资源也可以使用默认定义在POM中的（比如${project.version}），使用"-D"标志传递给命令行的（比如"-Dname=valuse"）或者明确使用properties元素定义的属性。过滤文件覆盖上面的这些属性。
- **directory：**这个元素的值定义了可以在哪里找到资源。构建默认的目录是${basedir}/src/main/resources。
- **includes：**一组文件模式，指定要作为资源包含在该指定目录下的文件，使用\*作为通配符。
- **excludes：**和includes相同的结构，但是指定的文件是要忽略掉的。在inlcude与exclude发生冲突时，exclude生效。
- **testResources：**testResources元素块包含testResource元素。它们的定义与resource元素相似，但是是在测试时期使用。一个区别是项目的默认（超级POM定义）测试资源目录是$ {basedir} / src / test / resources。测试资源是不被部署的。

#### Plugins
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <build>
    ...
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-jar-plugin</artifactId>
        <version>2.6</version>
        <extensions>false</extensions>
        <inherited>true</inherited>
        <configuration>
          <classifier>test</classifier>
        </configuration>
        <dependencies>...</dependencies>
        <executions>...</executions>
      </plugin>
    </plugins>
  </build>
</project>
```
除了标准坐标：groupId:artifactId:version，还有一些配置插件或与构建交互的元素。

- **extensions：**true或者false，是否加载此插件的扩展。默认为false。本文档稍后将介绍extensions。
- **inherited：**true或者false，该插件配置是否应适用于继承自此插件的POM。默认值为true。
- **configuration：**这是针对个别的插件。无需深入了解插件的工作原理，就可以在这里指定插件Mojo可能期望的任何属性（这些是Java Mojo bean中的getter和setter）。在上面的例子中，我们在maven-jar-plugin的Mojo中设置classifier的属性为test。可能要注意的是，所有configuration元素，无论它们在POM中的何处，都是将值传递给另一个底层系统，比如插件。换而言之：POM schema从不会明确要求cofiguration元素的值，但是一个插件目标有权要求这个配置值。
	如果您的POM声明一个父级，它将从父级的build / plugins或pluginManagement部分继承插件配置。
    
    为了说明，请考虑父POM中的以下片段：
    ```xml
    <plugin>
<groupId>my.group</groupId>
<artifactId>my-plugin</artifactId>
<configuration>
  <items>
    <item>parent-1</item>
    <item>parent-2</item>
  </items>
  <properties>
    <parentKey>parent</parentKey>
  </properties>
</configuration>
</plugin>
```
	并考虑使用该父项作为其父项目的以下插件配置：
```xml
<plugin>
<groupId>my.group</groupId>
<artifactId>my-plugin</artifactId>
<configuration>
  <items>
    <item>child-1</item>
  </items>
  <properties>
    <childKey>child</childKey>
  </properties>
</configuration>
```
	默认的行为是根据元素名称合并configuration元素的内容。如果子POM有一个特定的元素，该值将成为有效值。如果子POM没有一个元素，但是父POM有，则父值将成为有效值。请注意，这纯粹是对XML的操作;没有涉及插件本身的代码或配置。只涉及元素，而不是它们的值。
将这些规则应用到示例中，Maven提出：
```xml
<plugin>
<groupId>my.group</groupId>
<artifactId>my-plugin</artifactId>
<configuration>
  <items>
    <item>child-1</item>
  </items>
  <properties>
    <childKey>child</childKey>
    <parentKey>parent</parentKey>
  </properties>
</configuration>
```
	您可以通过向configuration元素的子级添加属性来控制子POM如何从父POM继承配置。这些属性是combine.children和combine.self。在子POM中使用这些属性来控制Maven如何将父项的插件配置与子项中的显式配置相结合。
以下是说明这个两个属性的子配置：
```xml
<configuration>
  <items combine.children="append">
    <!-- combine.children="merge" 是默认值 -->
    <item>child-1</item>
  </items>
  <properties combine.self="override">
    <!-- combine.self="merge" 是默认值 -->
    <childKey>child</childKey>
  </properties>
</configuration>
```
	现在，效果如下：
```xml
<configuration>
  <items combine.children="append">
    <item>parent-1</item>
    <item>parent-2</item>
    <item>child-1</item>
  </items>
  <properties combine.self="override">
    <childKey>child</childKey>
  </properties>
</configuration>
```
	**combine.children ="append"**导致父元素和子元素按顺序并列。另一方面**combine.self ="override"**完全抑制父配置。您不能同时使用**combine.self ="override"**和**combine.children ="append"**两者。如果你这样做，override会生效。
注意这些属性只能应用于声明它们的cofiguration元素上，而不会传播到嵌套元素。也就是说，如果来自子POM的item元素的内容是复杂的结构而不是文本，则其子元素仍然会受到默认合并策略的约束，除非它们本身也使用这两个属性标记。
	combine.\*属性从父级继承到子级POM。将这些属性添加到父POM时，请小心，因为这可能会影响到子级或次子级POM。

- **dependencies：**在POM中可以看到很多dependencies，并且是一个在所有plugins元素块下的元素。这个dependencies与在基本构建设置下的拥有相同的结构和功能。在这种情况下的主要区别在于，它们不再作为项目需要的依赖，而是作为其所属插件的依赖。这种差别的作用是可以修改一个插件的依赖列表，可能通过exclusions移除不使用的运行时期依赖，或通过更改所需要依赖的版本。
- **executions：**重要的是要记住，插件可能有多个目标。每个目标可能有一个单独的配置，甚至可能将插件的目标完全绑定到不同的阶段。executions配置插件目标的execution。
	比如，假设你想要绑定antrun:run目标到verify阶段。我们希望任务回显构建目录，并且通过设置inherited为false避免传递这个配置给它的子级（假设它是一个父级）。你会获得一个这样的execution：
    ```xml
    <project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <build>
    <plugins>
      <plugin>
        <artifactId>maven-antrun-plugin</artifactId>
        <version>1.1</version>
        <executions>
          <execution>
            <id>echodir</id>
            <goals>
              <goal>run</goal>
            </goals>
            <phase>verify</phase>
            <inherited>false</inherited>
            <configuration>
              <tasks>
                <echo>Build Dir: ${project.build.directory}</echo>
              </tasks>
            </configuration>
          </execution>
        </executions>
 
      </plugin>
    </plugins>
  </build>
</project>
```
	- **id：**自我解释。它在所有其他execution中指定了这个execution块。当运行配置的phase的时候，它将以以下形式显示：[plugin：goal execution：id]。在这个示例情况下是：[antrun:run execution: echodir]
	- **goals：**像所有多元化的POM元素一样，它包含单个元素的列表。在这种情况下，这个execution块指定的插件goals列表。
	- **phase：**这是指执行目标列表的阶段。这是一个非常强大的选项，允许将任何目标绑定到构建生命周期中的任何阶段，从而改变Maven的默认行为。   
	- **inherited：**像上面的inherited元素一样，设置它为false会抑制Maven把这个execution传递给它的子代。此元素仅对父POM有意义。
	- **configuration：**与上述相同，但将配置限制在此特定目标列表中，而不是插件下的所有目标。

#### PluginManagement
- **pluginManagement**是和plugins一起看到的元素。PluginManagement以非常相同的方式包含了plugin元素，除了它不是为了这个特定的项目构建而配插件信息，而是为了配置从这个项目继承的项目构建。但是，这只能配置子级中实际引用的plugins元素。子级有权重写pluginManagement定义。
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <build>
    ...
    <pluginManagement>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-jar-plugin</artifactId>
          <version>2.6</version>
          <executions>
            <execution>
              <id>pre-process-classes</id>
              <phase>compile</phase>
              <goals>
                <goal>jar</goal>
              </goals>
              <configuration>
                <classifier>pre-process</classifier>
              </configuration>
            </execution>
          </executions>
        </plugin>
      </plugins>
    </pluginManagement>
    ...
  </build>
</project>
```
如果我们将这些规范添加到plugins元素中，它们将仅应用于单个POM。但是，如果我们将它们应用在pluginManagement元素下，那么这个POM和所有继承的POM将maven-jar-plugin添加到构建中也将获得pre-process-classes的执行。所以相比将上面混乱的包含到每一个子pom.xml中，只需要在子级中这样添加：
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <build>
    ...
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-jar-plugin</artifactId>
      </plugin>
    </plugins>
    ...
  </build>
</project>
```
### Builde元素集
XSD中的Build类型表示这些元素仅用于“项目构建”。尽管有额外数目的元素（六个），实际只有两组元素是项目构建包含而配置文件构建缺失的：directories和 extensions.
#### Directories
directory元素集存在于build元素中，它为整个POM设置了各种目录结构。由于它们不存在于配置文件构建中，因此无法通过配置文件更改。
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <build>
    <sourceDirectory>${basedir}/src/main/java</sourceDirectory>
    <scriptSourceDirectory>${basedir}/src/main/scripts</scriptSourceDirectory>
    <testSourceDirectory>${basedir}/src/test/java</testSourceDirectory>
    <outputDirectory>${basedir}/target/classes</outputDirectory>
    <testOutputDirectory>${basedir}/target/test-classes</testOutputDirectory>
    ...
  </build>
</project>
```
如果上述\*Directory元素的值设置为绝对路径（当其属性展开时）那么使用该目录。否则，它是相对于基础构建目录：$ {basedir}。

#### Extensions
Extensions是在此构建中使用的artifact列表。它们将被包含在运行构建的classpath中。他们可以启用extensions给构建过程（例如为Wagon传输机制添加ftp提供程序）以及激活插件，从而对构建生命周期进行更改。简而言之，extensions是在构建期间激活的artifacts。extensions不需要实际执行任何操作，也不包含Mojo。因此，extensions对于指定一个通用插件接口的多个实现来说是非常好的。
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <build>
    ...
    <extensions>
      <extension>
        <groupId>org.apache.maven.wagon</groupId>
        <artifactId>wagon-ftp</artifactId>
        <version>1.0-alpha-3</version>
      </extension>
    </extensions>
    ...
  </build>
</project>
```
## 报告
### 报告设置

# 更多项目信息
## Licenses
## Organization
## Developers
## Contributors

# 环境设置
## 问题管理
## 
## Repositories
## Plugin Repositories

## Distribution Management
### Repository
### Site Distribution
### Relocation

## Profiles
### Activation
### BaseBuild元素集（重览）

# 终言