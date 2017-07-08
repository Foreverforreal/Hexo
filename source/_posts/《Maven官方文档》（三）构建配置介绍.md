title: 《Maven官方文档》（三）构建配置介绍
id: 1499394912889
author: 不识
tags:
  - Maven
categories:
  - Maven
  - 介绍
date: 2017-07-07 10:35:00
---
Apache Maven 2.0非常重视确保构建的可移植性。除了其他事项之外，这意味着允许在POM内构建配置，避免所有文件系统引用（在继承，依赖和其他地方），并且更倾向于本地仓库来存储使其成为可能的元数据。

然而，有时可移植性不是能完全做到。在某些情况下，插件可能需要配置本地文件系统路径。在其他情况下，需要稍微不同的依赖设置，并且项目的artifact名称可能需要稍微调整。而在其他时间，您甚至可能需要根据检测到的构建环境在构建生命周期中包含整个插件。

为了解决这些情况，Maven 2.0引入了构建配置文件的概念。配置文件使用POM本身可用的元素的子集指定（添加一个额外的部分），并以各种方式触发。它们在构建时期修改POM，并且意在在补充的设置中使用，为一系列目标环境提供等效但是不同的参数（提供例如开发，测试和生产环境中的应用程序服务器根目录的路径）。因此，配置文件很容易因为团队不同成员导致不同的构建结果。但是，正确使用，保持项目可移植性的同事配置文件也可以使用。这也将最小化使用maven的-f选项，该选项允许用户创建具有不同参数或配置的POM。这样使项目更易于维护，因为它仅与一个POM一起运行。
<!-- more -->
# 不同类型的配置是什么?每个都定义在哪里？
- 每个项目    
	--定义在POM本身（pom.xml）中。
- 每个用户   
   --定义在Maven设置(%USER\_HOME%/.m2/settings.xml)中
- 全局
   --定义在Maven全局设置（${maven.home}/conf/settings.xml）中。
- 配置描述
   --描述位于project basedir（profiles.xml）中（Maven 3.0中不受支持，请参阅Maven 3兼容性注释）
   
# 配置如何被触发？如何根据使用配置类型而变化？

可以通过以下几种方式触发/激活配置文件：
- 明确指定
- 通过Maven设置文件
- 基于环境变量
- OS设置
- 文件的存在或缺失

## 配置激活细节
可以使用-P 命令行界面选项显式指定配置文件。

此选项采用一个参数，该参数是以逗号分隔的profile-id列表。除了通过激活配置被激活或者砸setting.xml中&lt;activeProfiles>元素下的配置外，在指定这个选项时，选项参数中指定的配置也会被激活。
```
mvn groupId:artifactId:goal -P profile-1,profile-2
```
可以通过&lt;activeProfiles>部分，在Maven设置中激活配置文件。这部分是一个&lt;activeProfile>元素的列表，每个元素都包含一个profile-id。
```xml
<settings>
  ...
  <activeProfiles>
    <activeProfile>profile-1</activeProfile>
  </activeProfiles>
  ...
</settings>
```
默认情况下，每次一个项目使用它，列出在&lt;activeProfiles>标签中的配置文件将被激活。

配置会根据检测到的配置环境状态被自动触发。这些触发条件通过配置文件本身的&lt;activation>部分指定。目前，此检测限于JDK版本的前缀匹配，系统属性的存在或系统属性的值。这里有些例子。

当JDK的版本以“1.4”开头（例如“1.4.0\_08”，“1.4.2\_07”，“1.4”）时，以下配置将触发配置文件）：
```xml
<profiles>
  <profile>
    <activation>
      <jdk>1.4</jdk>
    </activation>
    ...
  </profile>
</profiles>
```
这种范围指定也可以用于Maven 2.1（有关详细信息，请参阅[ Enforcer Version Range Syntax](http://maven.apache.org/enforcer/enforcer-rules/versionRanges.html)）。以下指定1.3,1.4和1.5版本。
```xml
<profiles>
  <profile>
    <activation>
      <jdk>[1.3,1.6)</jdk>
    </activation>
    ...
  </profile>
</profiles>
```
注意：1.5]的上限很可能不包括1.5的大多数版本，因为它们将有一个额外的“补丁”版本，如\_05，在上述范围内不被考虑。

下一个将根据操作系统设置激活。有关操作系统值的更多详细信息，请参阅[Maven Enforcer Plugin](http://maven.apache.org/enforcer/enforcer-rules/requireOS.html)。
```xml
<profiles>
  <profile>
    <activation>
      <os>
        <name>Windows XP</name>
        <family>Windows</family>
        <arch>x86</arch>
        <version>5.1.2600</version>
      </os>
    </activation>
    ...
  </profile>
</profiles>
```

当系统属性“debug”被指定任何值的时候，下面的配置文件将被激活：
```xml
<profiles>
  <profile>
    <activation>
      <property>
        <name>debug</name>
      </property>
    </activation>
    ...
  </profile>
</profiles>
```
当系统属性“debug”根本没有定义时，将激活以下配置文件：
```xml

<profiles>
  <profile>
    <activation>
      <property>
        <name>!debug</name>
      </property>
    </activation>
    ...
  </profile>
</profiles>
```
当系统属性“debug”未定义或者使用不为“true”的值定义时，将激活以下配置文件。
```xml
<profiles>
  <profile>
    <activation>
      <property>
        <name>debug</name>
        <value>!true</value>
      </property>
    </activation>
    ...
  </profile>
</profiles>
```
要激活这个配置，您可以在命令行中键入其中之一：
```
mvn groupId:artifactId:goal
mvn groupId:artifactId:goal -Ddebug=false
```
当使用值“test”指定系统属性“environment”时，下一个示例将触发配置文件：
```xml
<profiles>
  <profile>
    <activation>
      <property>
        <name>environment</name>
        <value>test</value>
      </property>
    </activation>
    ...
  </profile>
</profiles>
```
要激活这个配置，您可以在命令行中键入：
```
mvn groupId:artifactId:goal -Denvironment=test
```
从Maven 3.0开始，POM中的配置文件也可以根据settings.xml中活动配置文件的属性激活。

注意：像FOO这样的环境变量可用作env.FOO格式的属性。还要注意，环境变量名称在Windows上被归一化为所有大写。

此示例当生成的文件target/generated-sources/axistools/wsdl2java/org/apache/maven缺失时，将触发该配置文件。
```xml
<profiles>
  <profile>
    <activation>
      <file>
        <missing>target/generated-sources/axistools/wsdl2java/org/apache/maven</missing>
      </file>
    </activation>
    ...
  </profile>
</profiles>
```
从Maven 2.0.9开始，可以标签&lt;exists>和&lt;missing>可以被插入值。支持的变量是系统属性，如${user.home}和环境变量，如${env.HOME}。请注意，POM本身定义的属性和值不可用于插值，例如，上述示例中激活器不能使用${project.build.directory}，需要对target路径进行硬编码。

使用如下配置，profile也可以默认激活。
```xml
<profiles>
  <profile>
    <id>profile-1</id>
    <activation>
      <activeByDefault>true</activeByDefault>
    </activation>
    ...
  </profile>
</profiles>
```
这个配置对于所有的构建默认激活，除非在相同POM中其他的配置使用了之前描述的方法被激活。当POM中的配置在命令行上或通过其激活配置被激活时，所有被社设置默认激活的配置将自动失效。

## 停用一个配置
从Maven 2.0.10开始，一个或多个配置可以使用命令行，通过将它们的识别码加上前缀符号'!'或'-'来停用，如下
```
mvn groupId:artifactId:goal -P !profile-1,!profile-2
```
这可以用于停用标记为activeByDefault的配置，或通过其激活配置激活的配置。


# 每种类型的配置可以自定义POM的哪些区域？为什么？
现在我们已经讨论了在哪里指定配置文件，以及如何激活它们，谈论您可以在配置文件中指定哪些内容将是有意义的。与配置文件配置的其他方面一样，这个答案并不简单。

根据您选择配置您的配置文件的位置，您将可以访问不同的POM配置选项。

## 外部文件中的Profiles
在外部文件中指定的配置（即在settings.xml或profiles.xml中）在最严格的意义上是不可移植的。任何似乎很有可能改变构建结果的东西都限于POM中的内联profile。像仓库列表这样的只能简单指定工件专有仓库，而不能改变构建结果。因此，您只能修改&lt;repositories>和&lt;pluginRepositories>部分，再加上额外的&lt;properties>部分。

&lt;properties>部分允许您指定自由格式键值对,这些属性将被包括在POM的插值过程中。这允许您以${profile.provided.path}的形式指定插件配置。

## POM中的Profiles

另一方面，如果您的配置文件有理由在在POM内部指定，那么你会有更多选项。当然，你只能修改该项目以及它子模块。由于这些配置文件是内联指定的，因此有更好的保留可移植性的机会，所以说可以向他们添加更多信息是合理的，而不会有其他用户无法使用该信息的风险。

POM中指定的配置Profiles 可以修改以下POM元素：
- &lt;repositories>
- &lt;pluginRepositories>
- &lt;dependencies>
- &lt;plugins>
- &lt;properties> (在主POM中实际上不可用，但在幕后使用)
- &lt;modules>
- &lt;reporting>
- &lt;dependencyManagement>
- &lt;distributionManagement>
- &lt;build>元素的子元集，其中包括：:
	- &lt;defaultGoal>
	- &lt;resources>
	- &lt;testResources>
	- &lt;finalName>

## &lt;profiles>之外的POM元素

我们不允许修改POM-profiles之外的一些POM元素，因为当POM部署到仓库系统时，不会分发这些运行时修改，就这导致个人的项目构建与其他人完全不一致。当您可以在某种程度上选择通过外部配置文件的进行此操作，这样危险性被限制了。另一个原因是这个POM信息有时从父POM中重用。

外部文件（如settings.xml和profiles.xml）也不支持POM-profiles之外的元素。让我们来看这个场景来进行阐述。当有效POM部署到远程存储库时，任何人都可以从存储库中获取其信息，并使用它直接构建Maven项目。现在假设，如果我们可以在依赖中设置配置-这对构建来说非常重要，或者在settings.xml中设置的POM-profiles之外的任何其他元素中，那么我们可能不能指望别人从仓库中使用该POM，并且能够构建它。而且我们还要考虑如何与其他人共享settings.xml。注意，太多的文件需要配置是非常混乱而且难以维护的。底线是因为这是构建数据所以它应该在POM中。Maven 2中的一个目标是将运行构建所需的所有信息合并到单个文件或作为POM的文件层次结构中。

# 配置陷阱
我们已经提到了为您的构建添加配置有可能破坏您的项目的可移植性。我们甚至已经突出强调了可能会破坏项目可移植性的情况。然而，值得重申的是作为对使用配置文件时避免的一些陷阱进行更相关讨论的一部分。
使用配置的时候要注意两个主要问题。第一个是外部属性，通常用在插件配置中。这些可能会破坏您的项目的可移植性。另一个更细微的地方是是profile的实际设置的不完整的规范。

## 外部属性
外部属性定义涉及在pom.xml之外定义的任何属性值，但不在其中对应的profile中定义。POM中最明显的属性使用是插件配置。虽然没有属性肯定有可能打破项目可移植性，但这些细节可能会产生微妙的影响，导致构建失败。例如，在settings.xml中指定的profile中指定Appserver路径可能会导致集成测试插件在组中的其他用户尝试在没有相似的settings.xml的情况下构建时失败。请考虑以下web应用程序项目的pom.xml片段：
```xml
<project>
  ...
  <build>
    <plugins>
      <plugin>
        <groupId>org.myco.plugins</groupId>
        <artifactId>spiffy-integrationTest-plugin</artifactId>
        <version>1.0</version>
        <configuration>
          <appserverHome>${appserver.home}</appserverHome>
        </configuration>
      </plugin>
      ...
    </plugins>
  </build>
  ...
</project>
```
现在，在你本地$ {user.home}/.m2/settings.xml中，你有：
```xml
<settings>
  ...
  <profiles>
    <profile>
      <id>appserverConfig</id>
      <properties>
        <appserver.home>/path/to/appserver</appserver.home>
      </properties>
    </profile>
  </profiles>
 
  <activeProfiles>
    <activeProfile>appserverConfig</activeProfile>
  </activeProfiles>
  ...
</settings>
```
当您构建integration-test生命周期阶段时，您的集成测试通过，因为您提供的路径允许测试插件安装和测试此Web应用程序。

但是，当您的同事尝试构建到integration-test时，他的构建失败是壮观的，抱怨说它无法解析插件配置参数&lt;appserverHome>,或者更糟糕的是，该参数的值 - 字面上是${appserver.home} - 是无效（如果它完全警告你）。

恭喜，您的项目现在不可移植。在pom.xml中加入此配置文件可以帮助缓和这种情况，因为每个项目层次结构（允许继承的效果）现在都必须指定此信息的明显缺陷。由于Maven提供对项目继承的良好支持，所以可以将这种配置放在团队级POM或类似文件中的&lt;pluginManagement>部分，并且只需简单继承路径。

另一个不那么有吸引力的答案可能是标准化的开发环境的。然而，这将倾向于危及Maven能够提供的生产力提升。

## 实际配置设置的不完整规范

除了上述的关于破坏移植性，很难覆盖到你的配置的所有情况。当您这样做时，您通常会将您的目标环境之一置于高处，干燥。我们从上面再来一个例子pom.xml片段：
```xml
<project>
  ...
  <build>
    <plugins>
      <plugin>
        <groupId>org.myco.plugins</groupId>
        <artifactId>spiffy-integrationTest-plugin</artifactId>
        <version>1.0</version>
        <configuration>
          <appserverHome>${appserver.home}</appserverHome>
        </configuration>
      </plugin>
      ...
    </plugins>
  </build>
  ...
</project>
```
现在，考虑以下profile，它将在pom.xml中内联指定：
```xml
<project>
  ...
  <profiles>
    <profile>
      <id>appserverConfig-dev</id>
      <activation>
        <property>
          <name>env</name>
          <value>dev</value>
        </property>
      </activation>
      <properties>
        <appserver.home>/path/to/dev/appserver</appserver.home>
      </properties>
    </profile>
 
    <profile>
      <id>appserverConfig-dev-2</id>
      <activation>
        <property>
          <name>env</name>
          <value>dev-2</value>
        </property>
      </activation>
      <properties>
        <appserver.home>/path/to/another/dev/appserver2</appserver.home>
      </properties>
    </profile>
  </profiles>
  ..
</project>
```
该配置与上一个示例类似，具有以下几个重要不同点：它明显地面向开发环境，添加了一个名为appserverConfig-dev-2的新配置，它有一个activation部分，当系统属性对于名为appserverConfig-dev的配置包含有"env=dev"以及对名为appserverConfig-dev-2的配置包含有 "env=dev-2"时，将触发其包含内容。所以，执行：
```xml
mvn -Denv=dev-2 integration-test
```
结果是成功构建，应用由配置名为appserverConfig-dev-2提供的属性。当我们执行
```
mvn -Denv=dev integration-test
```
它也会成功构建，并且应用由配置名为appserverConfig-dev提供的属性。然而，执行：
```
mvn -Denv=production integration-test
```
不会成功构建。为什么？因为，生成的非内插字面值${appserver.home}将不是用于部署和测试Web应用程序的有效路径。在写配置的时候我们没有考虑生产（production）环境的情况。“production”环境（env = production）以及“test”，甚至可能是“local”，构成了我们可能希望构建integration-test生命周期阶段的一套实际的目标环境。这个实际设置的不完整规范意味着我们将有效的目标环境限制在开发环境中。你的队友 - 也许你的经理 - 不会想看到这个幽默。当您构建配置文件以处理这些情况时，请务必解决整组目标排列。

简而言之，用户特定的配置文件可能以类似的方式运行。这意味着，当团队添加新的开发人员时，用于处理与用户密切关联的不同环境的配置可以起作用。虽然我认为这可以作为新手的有用的训练，但是以这种方式把他们扔向狼群并不好玩，一定要确保考虑到一整套的配置。

# 在构建过程中如何知道哪个配置有效？
确定活动配置将有助于用户知道构建过程中执行的特定配置。我们可以使用Maven Help Plugin来了解在构建期间什么配置有效。
```
  mvn help:active-profiles
```
让我们看一些小示例，这会帮助我们更多的理解这个插件的active-profiles目标。

从上一个在pom.xml中的配置示例，您会注意到有两个配置名为appserverConfig-dev和appserverConfig-dev-2，它们已被赋予不同的属性值。如果我们继续执行：
```
 mvn help:active-profiles -Denv=dev
 ```
结果将是具有“env = dev”的激活属性以及声明它的资源的配置的ID的项目符号列表。参见下面的示例。
```
The following profiles are active:
 
 - appserverConfig-dev (source: pom)
 ```
 
现在，如果我们在settings.xml中声明了一个配置（请参阅settings.xml中的配置文件示例），并将其设置为活动配置并执行：
```
  mvn help:active-profiles
```
The result should be something like this
中文(简体)
结果应该是这样的
```
The following profiles are active:
 
 - appserverConfig (source: settings.xml)
 ```
即使我们没有一个激活属性，这个配置也被列为是激活的。为什么？像我们之前提到的一样，在settings.xml中设置为active profile的配置将自动激活。

现在，如果我们有一个配置在setting.xml中被设置为active profile，同时也在POM中触发一个配置。你认为哪个配置会对构建产生影响？
```
 mvn help:active-profiles -P appserverConfig-dev
 ```
 这回列出激活配置列表
 ```
 The following profiles are active:
 
 - appserverConfig-dev (source: pom)
 - appserverConfig (source: settings.xml)
 ```
即使列出了两个活动的配置，但我们依然不确定其中哪一个已被应用。通过要看到对构建的影响，来执行
```
mvn help:effective-pom -P appserverConfig-dev
```
这将打印出该构建配置的有效POM到控制台。请注意，settings.xml中的配置比POM中的配置具有更高的优先级。以这里应用的配置是appserverConfig而不是appserverConfig-dev。

如果要将插件的输出重定向到名为effective-pom.xml的文件，请使用命令行选项-Doutput = effective-pom.xml。
# 命名约定
到目前为止，您已经注意到，配置是解决不同目标环境的不同构建配置要求问题的自然方式。以上，我们讨论了一个“实际设置”的概念来解决这种情况，以及考虑需要的整套配置文件的重要性。

然而，如何组织和管理这些设置的演化的问题也是不平凡的。正如一位好的开发人员努力编写个自记录代码一样，你的配置ID对您的预期用途至关重要。一个很好的方法是使用通用系统属性触发器作为配置的名称的一部分。这可能会导致env-dev，env-test和env-prod等名称被系统属性env触发的配置。这样的系统对如何激活针对特定环境的构建体提供了非常直观的提示。因此，要激活测试环境的构建，您需要通过以下方式激活env-test：
```
mvn -Denv=test <phase>
```
在配置id中，只需用“=”替换“ - ”即可使用正确的命令行选项。