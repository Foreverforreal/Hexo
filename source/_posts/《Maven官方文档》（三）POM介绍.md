title: 《Maven官方文档》（三）POM介绍
id: 1499178787072
author: 不识
tags:
  - Maven
  - 构建工具
categories:
  - Maven
  - 介绍
date: 2017-07-04 22:33:00
---
[原文链接](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)    
# POM是什么
一个项目对象模型或者POM是Maven的基本工作单元。它是一个XML文件，其中包含有关Maven用于构建项目的所有项目和配置详细信息。它包含了大多数项目的默认值。比如target是构建的目录；src/main/java是源代码目录；src/test/java是测试源代码目录。等等。    
POM从Maven 1中的project.xml重命名为Maven 2中的pom.xml。相比于有一个mave.xml文件包含可以执行的目标，现在目标或插件都配置在pom.xml中。当执行一个任务或目标，Maven查找当前目录的POM。它读取POM，获得需要的配置信息，然后执行目标。 

一些可以指定在POM中的配置是项目的依赖，可以执行的插件或目标，构建配置文件，等等。其他信息，比如项目版本，描述，开发者，邮件列表等等同样可以指定。

## 超级POM
超级POM是Maven的默认POM。除非明确设置，所有的POM扩展自超级POM，这意味着指定在超级POM中的配置由你为项目创建的POM继承。下面的代码片段是Maven 2.0.x的超级POM。
```xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <name>Maven Default Project</name>
 
  <repositories>
    <repository>
      <id>central</id>
      <name>Maven Repository Switchboard</name>
      <layout>default</layout>
      <url>http://repo1.maven.org/maven2</url>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
    </repository>
  </repositories>
 
  <pluginRepositories>
    <pluginRepository>
      <id>central</id>
      <name>Maven Plugin Repository</name>
      <url>http://repo1.maven.org/maven2</url>
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
    <directory>target</directory>
    <outputDirectory>target/classes</outputDirectory>
    <finalName>${artifactId}-${version}</finalName>
    <testOutputDirectory>target/test-classes</testOutputDirectory>
    <sourceDirectory>src/main/java</sourceDirectory>
    <scriptSourceDirectory>src/main/scripts</scriptSourceDirectory>
    <testSourceDirectory>src/test/java</testSourceDirectory>
    <resources>
      <resource>
        <directory>src/main/resources</directory>
      </resource>
    </resources>
    <testResources>
      <testResource>
        <directory>src/test/resources</directory>
      </testResource>
    </testResources>
  </build>
 
  <reporting>
    <outputDirectory>target/site</outputDirectory>
  </reporting>
 
  <profiles>
    <profile>
      <id>release-profile</id>
 
      <activation>
        <property>
          <name>performRelease</name>
        </property>
      </activation>
 
      <build>
        <plugins>
          <plugin>
            <inherited>true</inherited>
            <groupId>org.apache.maven.plugins</groupId>
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
            <groupId>org.apache.maven.plugins</groupId>
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
            <groupId>org.apache.maven.plugins</groupId>
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
下面的代码片段是Maven 2.1.x的超级POM。
```xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <name>Maven Default Project</name>
 
  <repositories>
    <repository>
      <id>central</id>
      <name>Maven Repository Switchboard</name>
      <layout>default</layout>
      <url>http://repo1.maven.org/maven2</url>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
    </repository>
  </repositories>
 
  <pluginRepositories>
    <pluginRepository>
      <id>central</id>
      <name>Maven Plugin Repository</name>
      <url>http://repo1.maven.org/maven2</url>
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
    <!-- TODO: MNG-3731 maven-plugin-tools-api < 2.4.4 expect this to be relative... -->
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
       <plugins>
         <plugin>
           <artifactId>maven-antrun-plugin</artifactId>
           <version>1.3</version>
         </plugin>       
         <plugin>
           <artifactId>maven-assembly-plugin</artifactId>
           <version>2.2-beta-2</version>
         </plugin>         
         <plugin>
           <artifactId>maven-clean-plugin</artifactId>
           <version>2.2</version>
         </plugin>
         <plugin>
           <artifactId>maven-compiler-plugin</artifactId>
           <version>2.0.2</version>
         </plugin>
         <plugin>
           <artifactId>maven-dependency-plugin</artifactId>
           <version>2.0</version>
         </plugin>
         <plugin>
           <artifactId>maven-deploy-plugin</artifactId>
           <version>2.4</version>
         </plugin>
         <plugin>
           <artifactId>maven-ear-plugin</artifactId>
           <version>2.3.1</version>
         </plugin>
         <plugin>
           <artifactId>maven-ejb-plugin</artifactId>
           <version>2.1</version>
         </plugin>
         <plugin>
           <artifactId>maven-install-plugin</artifactId>
           <version>2.2</version>
         </plugin>
         <plugin>
           <artifactId>maven-jar-plugin</artifactId>
           <version>2.2</version>
         </plugin>
         <plugin>
           <artifactId>maven-javadoc-plugin</artifactId>
           <version>2.5</version>
         </plugin>
         <plugin>
           <artifactId>maven-plugin-plugin</artifactId>
           <version>2.4.3</version>
         </plugin>
         <plugin>
           <artifactId>maven-rar-plugin</artifactId>
           <version>2.2</version>
         </plugin>        
         <plugin>                
           <artifactId>maven-release-plugin</artifactId>
           <version>2.0-beta-8</version>
         </plugin>
         <plugin>                
           <artifactId>maven-resources-plugin</artifactId>
           <version>2.3</version>
         </plugin>
         <plugin>
           <artifactId>maven-site-plugin</artifactId>
           <version>2.0-beta-7</version>
         </plugin>
         <plugin>
           <artifactId>maven-source-plugin</artifactId>
           <version>2.0.4</version>
         </plugin>         
         <plugin>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>2.4.3</version>
         </plugin>
         <plugin>
           <artifactId>maven-war-plugin</artifactId>
           <version>2.1-alpha-2</version>
         </plugin>
       </plugins>
     </pluginManagement>
  </build>
 
  <reporting>
    <outputDirectory>${project.build.directory}/site</outputDirectory>
  </reporting>
  <profiles>
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
            <groupId>org.apache.maven.plugins</groupId>
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
            <groupId>org.apache.maven.plugins</groupId>
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
            <groupId>org.apache.maven.plugins</groupId>
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

# 最小POM

POM的最低要求如下：
- project根元素
- modelVersion - 应该被设置为4.0.0
- groupId - 项目组织的id
- artifactId - 工件（项目）的id
- version - 指定组下的工件的版本

这有一个示例
```xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1</version>
</project>
```
POM需要配置其groupId，artifactId和version 。这三个值形成项目的完全合格的artifact名称。该名称是以 &lt;groupId>:&lt;artifactId>:&lt;version>的形式.对于上面的例子，其完全限定的工件名称是“com.mycompany.app:my-app:1”。

另外，如第一节所述，如果未指定配置详细信息，Maven将使用其默认值。这些默认值之一是打包类型。每个Maven项目都有一个packaging 类型。如果在POM中没有指定，那么将使用默认值“jar”。    
此外，您可以看到，在最小的POM中，没有指定repositories 。如果您使用最小的POM构建项目，它将继承超级POM中的repositories配置。
因此，当Maven看到最小POM中的依赖时，它会知道这些依赖将从超级POM中指定的http://repo.maven.apache.org/maven2下载。

# 项目继承
POM中的元素按以下合并

- dependencies
- developers and contributors
- plugin lists (including reports)
- plugin executions with matching ids
- plugin configuration
- resources

超级POM是项目继承的一个例子，但是，您还可以通过指定POM中的父元素来引入您自己的父POM，如以下示例所示。

## 示例1

**情景**
例如，让我们重新使用我们以前的工件com.mycompany.app:my-app:1。让我们再来介绍一个另外的工件，com.mycompany.app:my-module:1：
```xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-module</artifactId>
  <version>1</version>
</project>
```
让我们指定它们的目录结构如下：
```
.
 |-- my-module
 |   `-- pom.xml
 `-- pom.xml
 ```
 **注意**:my-module/pom.xml是 com.mycompany.app:my-module:1的POM，而pom.xml是of com.mycompany.app:my-app:1的POM。

**解决方案**
现在，如果我们将com.mycompany.app:my-app:1转换成com.mycompany.app:my-module:1的父工件，我们需要按以下配置来修改com.mycompany.app:my-module:1的POM：

**com.mycompany.app:my-module:1的POM**
```xml
<project>
  <parent>
    <groupId>com.mycompany.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1</version>
  </parent>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-module</artifactId>
  <version>1</version>
</project>
```
 注意现在我们添加的部分-&lt;parent>元素。这部分允许我们指定哪些工件是我们的POM的父级。我们通过指定父POM的完全限定的工件名称来实现。通过此设置，我们的模块现在可以继承我们的父POM的一些属性。     
另外，如果我们希望你的模块的goupId和/或version与它的父级的一样，你可以在其POM中删除该组件的groupId和/或version。
```xml
<project>
  <parent>
    <groupId>com.mycompany.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1</version>
  </parent>
  <modelVersion>4.0.0</modelVersion>
  <artifactId>my-module</artifactId>
</project>
```

这允许模块继承它的父POM的groupId和version。

## 示例2
**情景**   
但是，如果父项目已经安装在本地仓库中，或者是在特定的目录结构（父pom.xml是一个比模块的pom.xml更高的目录）中，示例1会起作用。

但是如果父级没有安装并且目录结构如下会如何呢
```
 |-- my-module
 |   `-- pom.xml
 `-- parent
     `-- pom.xml
 ```
 
**解决方案**
要解决这个目录结构（或任何其他目录结构），我们必须将&lt;relativePath>元素添加到我们的父部分。
```xml
<project>
  <parent>
    <groupId>com.mycompany.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1</version>
    <relativePath>../parent/pom.xml</relativePath>
  </parent>
  <modelVersion>4.0.0</modelVersion>
  <artifactId>my-module</artifactId>
</project>
```
顾名思义，它是从模块的pom.xml到父的pom.xml的相对路径。

# 项目聚合
项目聚合与项目继承类似。但是相比于从模块中指定父POM，它从父POM中指定模块。通过这样做，父项目现在知道其模块，并且如果针对父项目调用Maven命令，那么该Maven命令也将被执行到父项目的模块。要进行项目聚合，您必须执行以下操作：

- 将父POM的packaging更改为值“pom”。
- 在父POM中指定其模块的目录（子级POM）

## 示例3
**情景**
给定以前的原始工件POM和目录结构，
**com.mycompany.app:my-app:1的POM**
```xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1</version>
</project>
```
**com.mycompany.app:my-module:1的POM**
```xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-module</artifactId>
  <version>1</version>
</project>
```

**目录结构**
```xml
.
 |-- my-module
 |   `-- pom.xml
 `-- pom.xml
```
**解决方案**
如果我们将my-module聚合到my-app中，我们只需要修改 my-app。
```xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1</version>
  <packaging>pom</packaging>
 
  <modules>
    <module>my-module</module>
  </modules>
</project>
```
在修订的com.mycompany.app:my-app:1中，添加了packaging部分和modules部分。对于packaging，其值设置为“pom”，对于modules部分，我们有元素&lt;module>my-module&lt;/module>。&lt;module>的值是从com.mycompany.app:my-app:1到com.mycompany.app:my-module:1的POM的相对路径(通过实践，我们使用模块的artifactId作为模块目录的名称)。

现在，每当Maven命令处理com.mycompany.app:my-app:1时，同样的Maven命令也将针对com.mycompany.app:my-module:1运行。此外，一些命令（具体目标）不同地处理项目聚合。

## 示例4

**情景**
但是如果将目录结构更改为以下内容呢？
```
.
 |-- my-module
 |   `-- pom.xml
 `-- parent
     `-- pom.xml
```
父pom如何指定其模块？

**解决方案**
答案？ - 与示例3相同的方式，通过指定模块的路径。
```xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1</version>
  <packaging>pom</packaging>
 
  <modules>
    <module>../my-module</module>
  </modules>
</project>
```


# 项目继承vs项目聚合

如果您有多个Maven项目，并且它们都具有相似的配置，那么可以通过抽取这些类似配置并建立一个父项目来重构项目。因此，您所要做的就是让Maven项目继承父项目，然后将这些配置会应用于所有这些项目。    
如果您有一组项目一起构建或处理，您可以创建一个父项目，并让该父项目将这些项目声明为其模块。通过这样做，您只需要构建父级，其余的将遵循。    
但是当然，您可以同时拥有项目继承和项目聚合。意思是，您可以让模块指定一个父项目，同时让该父项目指定这些Maven项目作为其模块。你只需要应用三个规则：

- 指定每一个子POM谁是它们的父POM
- 修改父POM的packaging为“pom”
- 在父POM中指定其模块（子级POM）的目录

## 示例5

再次给定之前的原始工件POM

**com.mycompany.app:my-app:1的POM**
```xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1</version>
</project>
```
**com.mycompany.app:my-module:1的POM**
```xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-module</artifactId>
  <version>1</version>
</project>
```
以及目录结构
```
.
 |-- my-module
 |   `-- pom.xml
 `-- parent
     `-- pom.xml
```

**解决方案**
要进行项目继承和聚合，您只需应用所有三个规则。


**com.mycompany.app:my-app:1的POM**
```xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1</version>
  <packaging>pom</packaging>
 
  <modules>
    <module>../my-module</module>
  </modules>
</project>
```
**com.mycompany.app:my-module:1的POM**
```xml
<project>
  <parent>
    <groupId>com.mycompany.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1</version>
    <relativePath>../parent/pom.xml</relativePath>
  </parent>
  <modelVersion>4.0.0</modelVersion>
  <artifactId>my-module</artifactId>
</project>
```
**注意**：配置文件继承与POM本身使用的相同的继承策略。

# 项目插值和变量
Maven鼓励的做法之一是不要重复。但是，在某些情况下，您需要在几个不同的位置使用相同的值。为了帮助确保该值只被指定一次，Maven允许您在POM中使用自己的和预定义的变量。  
例如，要访问project.version变量，您可以这样引用它：
```
<version>${project.version}</version>
```
需要注意的一点是，这个变量在继承之后才能如上进行处理。这意味着如果一个父项目使用一个变量，然后它定义在子级中，而不是父级，这完全可以使用。
## 可用变量
**项目模型变量**   
模型的任何字段都是一个单值元素，可以作为一个变量引用。例如${project.groupId}，${project.version}，${project.build.sourceDirectory}等等。请参阅{% post_link 《Maven官方文档》（三）POM参考 %}_以查看属性的完整列表。   
这些变量全部由前缀“project”引用。你也可能看到使用"pom."作为前缀引用，或者前缀全部省略-这些形式现在被弃用，并且也不应该再被使用。

**特殊变量**

|||
|--|--|
|project.basedir|当前项目所在的目录。|
|project.baseUri|当前项目所在的目录，表示为URI。自从Maven 2.1.0|
|maven.build.timestamp|表示构建开始的时间戳。自从Maven 2.1.0-M1|

可以通过声明属性maven.build.timestamp.format来定制构建时间戳的格式，如下面的示例所示：

```xml
<project>
  ...
  <properties>
    <maven.build.timestamp.format>yyyy-MM-dd'T'HH:mm:ss'Z'</maven.build.timestamp.format>
  </properties>
  ...
</project>
```
格式模式必须符合SimpleDateFormat API文档中给出的规则。如果属性不存在，则默认格式为示例中给出的值。

**属性**
您还可以将项目中定义的任何属性引用为变量。请考虑以下示例：
```xml
<project>
  ...
  <properties>
    <mavenVersion>2.1</mavenVersion>
  </properties>
  <dependencies>
    <dependency>
      <groupId>org.apache.maven</groupId>
      <artifactId>maven-artifact</artifactId>
      <version>${mavenVersion}</version>
    </dependency>
    <dependency>
      <groupId>org.apache.maven</groupId>
      <artifactId>maven-project</artifactId>
      <version>${mavenVersion}</version>
    </dependency>
  </dependencies>
  ...
</project>
```