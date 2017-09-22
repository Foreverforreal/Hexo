title: 《Maven官方文档》（二）构建生命周期介绍
id: 1499178889748
author: 不识
tags:
  - Maven
categories:
  - Maven
  - 介绍
date: 2017-07-04 22:34:00
---
# 构建生命周期基础
Maven是基于构建生命周期核心概念。这意味着构建和分发一个特定artifact（项目）的过程是被明确定义的。    
对于构建项目的人员，这意味着只需要学习一小堆命令就能构建任何Maven项目，并且POM将确保他们获得所需的结果。   
这有三个内置的生命周期：**default**，**clean**和**site**。**default**生命周期处理你的项目部署，**clean**生命周期处理你的项目清除，而**site**生命周期处理你的项目站点文档的创建。 

<!-- more -->

## 构建生命周期由阶段组成
每一个这些生命周期都由不同的构建阶段列表定义，其中构建阶段表示生命周期中的时期。   
比如，default生命周期包含下几个阶段（有关生命周期阶段的完整列表，请参阅[生命周期参考]()）
- **validate - **验证项目是否正确，所有必要的信息是否可用
- **compile - **编译项目的源代码
- **test - **使用合适的单元测试框架测试编译的源代码。这些测试不应该要求代码被打包或部署
- **package - **编译代码并将其打包成可分发格式，如JAR。
- **verify - **对整合测试结果进行任何检查，以确保满足质量标准
- **install - **将软件包安装到本地仓库中，以作为本地其他项目的依赖使用
- **deploy - **在构建环境中完成，将最终软件包复制到远程仓库，以与其他开发人员和项目共享。

这些生命周期阶段（加上这里未显示的其他生命周期阶段）按顺序执行，以完成default周期。给定上述生命周期阶段，这意味着当使用default生命周期时，Maven将首先对项目进行验证，然后尝试编译源代码，针对这些源代码进行测试，打包二进制文件（例如jar），针对这些包进行集成测试，验证集成测试，将验证的软件包安装到本地仓库，然后将安装的软件包部署到远程仓库。

## 通常的命令行调用
在开发环境中，使用以下调用来构建并将artifact安装到本地仓库中。
```
mvn install
```
该命令在执行install之前，按顺序(validate, compile, package,等),执行每个默认生命周期阶段。你只需要调用最后的构建阶段来执行，在这个例子中是install：
在构建环境中，使用以下调用来清空构建，并将artifact发布到共享仓库中。
```
mvn clean deploy
```
相同的命令可以在多模块场景（即具有一个或多个子项目的项目）中使用。Maven遍历每个子项目并执行clean，然后执行deploy（包括所有之前的构建阶段步骤）。

## 构建阶段由插件目标组成
然而，即使构建阶段负责构建生命周期中的特定步骤，其执行这些职责的方式可能会有所不同。这是通过声明绑定到这些构建阶段的插件目标来完成的。   
一个插件目标表示一个指定有助于构建和管理项目的任务（比构建阶段更精细）。目标可能会被绑定到零个或多个构建阶段上。一个目标没有被绑定到任何构建阶段上的话，可以通过直接调用在构建生命周期之外来执行。执行顺序取决于调用目标和构建阶段的顺序。例如，考虑下面的命令。clean和package参数是构建阶段，而dependency:copy-dependencies是一个（插件的）目标。    
```
mvn clean dependency:copy-dependencies package
```
如果这个命令执行的话，clean阶段会首先被执行（这意味着它将运行clean 生命周期的所有前期阶段，以及clean 阶段本身），然后执行dependency:copy-dependencies 目标,最后执行package阶段（以及default 生命周期中的所有之前的构建阶段）。   
而且，如果一个目标被绑定到一个或多个构建阶段，那么在所有这些阶段都将调用这个目标。    

此外，构建阶段也可以有零个或多个目标。如果构建阶段没有绑定目标，则该构建阶段将不会执行。但是，如果它有一个或多个目标，它将执行所有这些目标。  

（注意：在Maven 2.0.5及更高版本中，绑定到阶段的多个目标的执行顺序与POM中声明的顺序相同，但是不支持同一插件的多个实例。同一个插件的多个实例被分组以一起执行，并在Maven 2.0.11及更高版本中进行排序）

## 某些阶段通常不会从命令行调用
用连字符（pre \*，post- \*或process- \*）命名的阶段通常不会从命令行直接调用。这些阶段对构建进行排序，生成在构建之外无用的中间结果。在调用integration-test的情况下，环境可能处于挂起状态。  
代码覆盖工具比如Jacoco，以及执行容器插件比如Tomcat，Cargo和Docker绑定目标到pre-integration-test阶段以准备集成测试容器环境。这些插件也绑定目标到post-integration-test阶段以收集覆盖统计数据或停止集成测试容器。   
故障安全和代码覆盖插件将目标绑定到integration-test和verify 阶段。最终结果是在verify 阶段后可以使用测试和覆盖率报告。如果从命令行调用integration-test，则不会生成任何报告。更为糟糕的是集成测试容器环境处于挂起状态；Tomcat网络服务器或Docker实例仍在运行，Maven甚至可能不会自行终止。

# 建立项目以使用构建生命周期

构建生命周期足够简单，但是当您为项目构建一个Maven构建时，您如何将任务分配到每个构建阶段？
## Packaging
第一种也是最常见的方法是通过相同命名的POM元素&lt;packaging>为您的项目设置packaging。packaging元素一些有效的值由：jar，war，ear，和pom。如果没有指定packaging的值，默认是jar。
每个packaging包含绑定到特定阶段的一系列目标。比如，jar将绑定以下目标到default生命周期的构建阶段。

|||
|-|-|
|process-resources		|resources:resources	|
|compile				|compiler:compile		|
|process-test-resources	|resources:testResources|
|test-compile			|compiler:testCompile	|
|test					|surefire:test			|
|package				|jar:jar				|
|install				|install:install		|
|deploy					|deploy:deploy			|

这是一个几乎是标准的绑定;然而，一些packagings以不同的方式处理它们。比如一些项目纯粹是元数据（packaging值是pom）只会绑定目标到install和deploy阶段（对于绑定到一些packaging类型的完整goal-to-build-phase 列表，参阅[“生命周期参考”](http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#Lifecycle_Reference)）.

请注意，对于某些可用的打包类型，您可能还需要在POM的&lt;build>部分中包含一个特定的插件，并为该插件指定&lt;extensions> true </ extensions>。
需要这样的插件的一个例子是Plexus插件，它提供plexus-application和plexus-service的packaging.

## Plugins
将目标添加到阶段的第二种方法是在项目中配置插件。插件是提供目标给Maven的artifact。此外，一个插件可能有一个或多个目标，其中每个目标都表示这个插件的一个能力。比如，编译插件有两个目标：compile和testCompile。前一个编译你的主代码的源代码，而后一个编译你的测试代码的源代码。    
正如你将在后面的部分将会看到的，插件可以包含将目标绑定到哪个生命周期阶段的信息。注意，只添加插件自身是不够的-你还必须指定你想运行的目标作为构建的哪一部分。    
配置的目标将被添加到已经从选定的包装绑定到生命周期的目标。如果超过一个目标被绑定到一个特定的阶段，则使用的顺序是首先执行来自packaging 的顺序，然后执行在POM中配置的那些。请注意，您可以使用&lt;executions>元素来获得对特定目标的顺序的更多控制。 

例如，Modello插件默认将目标modello：java绑定到generate-sources阶段（注：modello：java目标生成Java源代码）。所以要使用Modello插件，并从模型中生成源代码并将其并入到构建中，您将在&lt;build>的&lt;plugins>部分中将以下内容添加到POM中：
```xml
...
 <plugin>
   <groupId>org.codehaus.modello</groupId>
   <artifactId>modello-maven-plugin</artifactId>
   <version>1.8.1</version>
   <executions>
     <execution>
       <configuration>
         <models>
           <model>src/main/mdo/maven.mdo</model>
         </models>
         <version>4.0.0</version>
       </configuration>
       <goals>
         <goal>java</goal>
       </goals>
     </execution>
   </executions>
 </plugin>
...
```
你可能会想知道为什么&ltexecutions>元素在这。这样，如果需要，您可以使用不同的配置多次运行相同的目标。可以使用给定ID区分execution，以便在继承或应用配置文件期间，您可以控制是否将目标配置合并或转换为额外的execution。    
当给定多个与特定阶段匹配的execution时，它们按照POM中指定的顺序执行，继承的execution首先运行。    
现在，在modello：java的情况下，它只在generate-sources阶段有意义。但是一些目标可以用在超过一个阶段，也许这可能没有合理的默认。对于那些，您可以自己指定阶段。例如，假设你有一个目标display:time，它辉县目前的时间到命令行，并且你想要它运行在process-test-resources阶段指出测试何时开始。这将被配置如下：
```xml
...
 <plugin>
   <groupId>com.mycompany.example</groupId>
   <artifactId>display-maven-plugin</artifactId>
   <version>1.0</version>
   <executions>
     <execution>
       <phase>process-test-resources</phase>
       <goals>
         <goal>time</goal>
       </goals>
     </execution>
   </executions>
 </plugin>
...
```
# 生命周期参考
以下列出了default,clean和site生命周期的所有构建阶段，它们按照指定的顺序执行。
**Clean生命周期**

|||
|--|--|
|pre-clean|执行实际项目清理之前所需的流程|
|clean|删除以前构建生成的所有文件|
|post-clean|执行完成项目清理所需的过程|

**Default生命周期**

|||
|--|--|
|validate|验证项目是否正确，所有必要的信息是否可用。|
|initialize|初始化构建状态，例如设置属性或创建目录。|
|generate-sources|生成包含在编译中的任何源代码。|
|process-sources|处理源代码，例如过滤任何值。|
|generate-resources|生成包含在包中的资源。|
|process-resources|将资源复制并处理到目标目录中，准备打包。|
|compile|编译项目的源代码。|
|process-classes|后期处理编译生成的文件，例如对Java类进行字节码增强。|
|generate-test-sources|生成包含在编译中的任何测试源代码。|
|process-test-sources|处理测试源代码，例如过滤任何值。|
|generate-test-resources|创建测试资源。|
|process-test-resources|将资源复制并处理到测试目标目录中。|
|test-compile|将测试源代码编译到测试目标目录中|
|process-test-classes|后期处理测试编译生成的文件，例如对Java类进行字节码增强。对于Maven 2.0.5及以上版本。|
|prepare-package|在实际打包前，执行任何必要的准备打包操作。这会产生一个解压，处理版本的包（Maven 2.1及以上）|
|package|使用编译后的代码，并以其可分发的格式（如JAR）进行打包。|
|pre-integration-test|在执行集成测试之前执行所需的操作。这可能涉及诸如设置所需环境等事情。|
|integration-test|如果必要时将包处理并部署到集成测试运行的环境|
|post-integration-test|执行集成测试后执行所需的操作。这可能包括清理环境。|
|verify|运行任何检查以验证包是否有效符合质量标准。|
|install|将软件包安装到本地仓库中，以作为本地其他项目的依赖。|
|deploy|在集成或发布环境中完成，将最终软件包复制到远程存储库，以便与其他开发人员和项目共享。|

**Site生命周期**

|||
|--|--|
|pre-site|在实际的项目站点生成之前执行所需的执行的流程|
|site|生成项目的站点文档|
|post-site|执行完成站点生成所需的进程，并准备站点部署|
|site-deploy|将生成的站点文档部署到指定的Web服务器|

# 内置生命周期绑定
一些阶段默认情况下已经绑定了一些目标。对于default生命周期，这些绑定依赖packagin的值。这有一些goal-to-build-phase 绑定。

**Clean生命周期绑定**

|||
|--|--|
|clean|clean:clean|

**Default生命周期绑定- Packaging ejb / ejb3 / jar / par / rar / war**

|||
|--|--|
|process-resources|	resources:resources|
|compile|	compiler:compile|
|process-test-resources|	resources:testResources|
|test-compile|	compiler:testCompile|
|test|	surefire:test|
|package|	ejb:ejb or ejb3:ejb3 or jar:jar or par:par or rar:rar or war:war|
|install|	install:install|
|deploy	|deploy:deploy|

**Default生命周期绑定- Packaging ear**

|||
|--|--|
|generate-resources	|ear:generate-application-xml
|process-resources	|resources:resources
|package|	ear:ear
|install|	install:install
|deploy	|deploy:deploy

**Default生命周期绑定- Packaging maven-plugin**

|||
|--|--|
|generate-resources	|plugin:descriptor
|process-resources|	resources:resources
|compile	|compiler:compile
|process-test-resources|	resources:testResources
|test-compile	|compiler:testCompile
|test|	surefire:test
|package|	jar:jar and plugin:addPluginArtifactMetadata
|install|	install:install
|deploy	|deploy:deploy

**Default生命周期绑定- Packaging pom**

|||
|--|--|
|package|	site:attach-descriptor
|install|	install:install
|deploy|	deploy:deploy

**Site 生命周期绑定**

|||
|--|--|
|site	|site:site|
|site-deploy|	site:deploy|

# 参考
完整的Maven生命周期由maven-core模块中的components.xml文件定义，并附有相关文档供参考。    
在Maven 2.x中，components.xml中包含默认的生命周期绑定，但在Maven 3.x中，它们在单独的default-bindings.xml描述符中定义。