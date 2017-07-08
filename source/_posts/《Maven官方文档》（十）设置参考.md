title: 《Maven官方文档》（十）设置参考
id: 1499051073985
author: 不识
tags:
  - Maven
  - 构建工具
categories:
  - Maven
  - 参考
date: 2017-07-03 11:04:00
---
# 简介
## 快速概览
setting.xml文件中的setting元素包含了用于定义以各种方式配置Maven执行的值的元素，比如pom.xml，但是不应该捆绑到任何具体的项目或分发给用户。它包含一些值，比如本地仓库的位置，可选的远程仓库服务器，以及验证信息。    
setting.xml文件可能存在于两个位置：
1. Maven安装目录下：$ {maven.home} /conf/settings.xml
2. 用户安装目录： ${user.home}/.m2/settings.xml
<!-- more -->
前一个setting.xml同时也被称为全局设置，后一个settin.xml被称为用户设置。如果两个文件都存在，会以用户指定的setting.xml为主，将两者的内容合并。   
提示：如果你需要从头开始创建用户指定设置，从你的Maven安装中复制全局设置到你的${user.home}/.m2目录是最简单的。Maven的默认setting.xml也是一个包含了注释和示例模板，使你可以快速调整以满足你的需求。
下面是setting下顶级元素的概览：
```xml
 <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                          https://maven.apache.org/xsd/settings-1.0.0.xsd">
      <localRepository/>
      <interactiveMode/>
      <usePluginRegistry/>
      <offline/>
      <pluginGroups/>
      <servers/>
      <mirrors/>
      <proxies/>
      <profiles/>
      <activeProfiles/>
    </settings>
```
setting.xml的内容可以使用以下表达式进行插入值：
1. ${user.home}和其他的系统属性（自从Maven 3.0）
2. ${env.HOME}等环境变量

注意，setting.xml中定义在profiles中的属性不可以用来当作插值。

# 设置详细信息

## 简单值
一般的顶级setting元素都是一些简单的值，表示一系列用来描述构建系统全时间活跃的元素的值。
```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                      https://maven.apache.org/xsd/settings-1.0.0.xsd">
  <localRepository>${user.home}/.m2/repository</localRepository>
  <interactiveMode>true</interactiveMode>
  <usePluginRegistry>false</usePluginRegistry>
  <offline>false</offline>
  ...
</settings>
```
- **localRepository**：这个值是构建系统本地仓库的路径。它的默认值是${user.home}/.m2/repository。该元素对主构建服务器允许所有登录用户从公共本地仓库构建非常有用。
- **interactiveMode**：如果Maven应尝试与用户进行交互以进行输入，则为true。否则为false。默认是true。
- **userPluginRegistry**：如果Maven使用${user.home}/.m2/plugin-registry.xml文件来管理插件版本，则为true。默认为false。注意当前的Maven 2.0版本，不应该依赖于plugin-registry.xml文件。目前认为它无效。
- **offline**：如果此构建系统应在离线模式下运行，则为true，默认为false。这个元素对于由于网络设置或者安全原因无法连接到远程仓库的构建服务器很有用。

## 插件组
该元素包含一个pluginGroup元素的列表，其中每一个都包含一个groupId。当一个插件被使用并且groupId没有在命令行上提供时，会在这个列表中搜索。这个列表自动包含org.apache.maven.plugins和org.codehaus.mojo。

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                      https://maven.apache.org/xsd/settings-1.0.0.xsd">
  ...
  <pluginGroups>
    <pluginGroup>org.mortbay.jetty</pluginGroup>
  </pluginGroups>
  ...
</settings>
```
比如，给定的上述设置，Maven命令行可能使用缩短的命令来执行org.mortbay.jetty:jetty-maven-plugin:run
```
mvn jetty：run
```
## 服务器
用于下载和部署的仓库由POM的repositories和distributionManagement元素定义。但是，某些设置（如username和password）不应与pom.xml一起分发。这种类型的信息应该存在于的构建服务器的settings.xml中。
```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                      https://maven.apache.org/xsd/settings-1.0.0.xsd">
  ...
  <servers>
    <server>
      <id>server001</id>
      <username>my_login</username>
      <password>my_password</password>
      <privateKey>${user.home}/.ssh/id_dsa</privateKey>
      <passphrase>some_passphrase</passphrase>
      <filePermissions>664</filePermissions>
      <directoryPermissions>775</directoryPermissions>
      <configuration></configuration>
    </server>
  </servers>
  ...
</settings>
```
- **id** 这是服务器的ID（不是用户用来登录的），它与Maven尝试连接的仓库/镜像的id元素相匹配。
- **username, password**：这些元素成对出现，它表示向该服务器进行验证所需的登录名和密码。
- **privateKey, passphrase**：像前面的两个元素，这对元素指定一个私钥的路径（默认是${user.home}/.ssh/id\_dsa）和一个密码，如果需要的话。passphrase和password元素在将来可能会外部化，但是现在他们必须在setting.xml中以纯文本设置。
- **filePermissions, directoryPermissions**：当在部署时创建仓库文件或目录时，这些是使用的权限。每个的合法值是一个三位数的数字对应于\* nix文件的权限，即。 664或775。

注意：如果使用私钥登录服务器，请确保省略&lt;password>元素。否则，密钥将被忽略。
### 密码加密
一个新功能 - 服务器密码和密码加密已经添加到2.1.0+。详细信息请查看[“密码加密”](http://maven.apache.org/guides/mini/guide-encryption.html)
## 镜像
```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                      https://maven.apache.org/xsd/settings-1.0.0.xsd">
  ...
  <mirrors>
    <mirror>
      <id>planetmirror.com</id>
      <name>PlanetMirror Australia</name>
      <url>http://downloads.planetmirror.com/pub/maven2</url>
      <mirrorOf>central</mirrorOf>
    </mirror>
  </mirrors>
  ...
</settings>
```
- **id, name: **该镜像的唯一标识符和用户友好名称。该id用于区分mirror元素，并在连接到镜像时从&lt;servers>部分选择相应的凭据。
- **url:**镜像的基础URL。构建系统会使用这个URL来连接到一个远程仓库，而不是使用原始的仓库URL。
- **mirrorOf：**要镜像的仓库的id。比如，要指向Maven central仓库（https://repo.maven.apache.org/maven2） 的镜像，设置这个元素值为central。更高级的映射，如repo1，repo2或\*，！inhouse也是可能的。但是这个不能匹配一个镜像的id。

要更深入地介绍镜像，请阅读[“镜像设置指南”](http://maven.apache.org/guides/mini/guide-mirror-settings.html)。


## 代理
```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                      https://maven.apache.org/xsd/settings-1.0.0.xsd">
  ...
  <proxies>
    <proxy>
      <id>myproxy</id>
      <active>true</active>
      <protocol>http</protocol>
      <host>proxy.somewhere.com</host>
      <port>8080</port>
      <username>proxyuser</username>
      <password>somepassword</password>
      <nonProxyHosts>*.google.com|ibiblio.org</nonProxyHosts>
    </proxy>
  </proxies>
  ...
</settings>
```
- **id：**此代理的唯一标识符。这用于区分proxy元素。
- **active：**如果此代理是开启的，则为true。这用于声明一系列的代理，但是一次只有一个代理开启可用。
- **protocol, host, port：**代理protocol://host:port ，分割开成不同的元素。
- **username, password:**这些元素成对出现，表示向该代理服务器进行身份验证所需的登录名和密码。
- **nonProxyHosts: **这是不应该被代理的主机列表。列表的分隔符是代理服务器的预期的类型;上面的例子是管道分隔 - 逗号分隔也是常见的。

## Profiles
settings.xml中的profile元素是pom.xml中的profile元素的缩短版本。它由activation, repositories, pluginRepositories和properties元素组成。profile元素只包含这四个元素，因为它们与整个构建系统（这是settings.xml文件的作用）有关，而不是关于单个项目对象模型设置。    
如果profile在setting中处于开启状态，则其值将覆盖POM或profiles.xml文件中的任何等同ID的配置文件。
### Activation
Activations是profile的关键。像POM的profiles一样，profiles的功能来自于在某些情况下修改某些值的能力;这些情况通过activation元素指定。
```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                      https://maven.apache.org/xsd/settings-1.0.0.xsd">
  ...
  <profiles>
    <profile>
      <id>test</id>
      <activation>
        <activeByDefault>false</activeByDefault>
        <jdk>1.5</jdk>
        <os>
          <name>Windows XP</name>
          <family>Windows</family>
          <arch>x86</arch>
          <version>5.1.2600</version>
        </os>
        <property>
          <name>mavenVersion</name>
          <value>2.0.3</value>
        </property>
        <file>
          <exists>${basedir}/file2.properties</exists>
          <missing>${basedir}/file1.properties</missing>
        </file>
      </activation>
      ...
    </profile>
  </profiles>
  ...
</settings>
```
当满足所有指定的标准时，Activation会开启，尽管不是所有的都是必需的。
- **jdk：**activation有一个内置的，以Java为中心的jdk元素的检查。如果测试在与给定的前缀匹配的jdk版本号下运行，它会激活。在上面的例子中，1.5.0\_06将匹配。
- **os：**os元素可以定义上面显示的一些操作系统特定属性。有关操作系统值的更多详细信息，请参阅[maven-enforcer-plugin](https://maven.apache.org/plugins/maven-enforcer-plugin/rules/requireOS.html)。
- **property：**如果Maven检测到对应的name=value对的属性（可以通过${name}在POM中取消引用的值），该profile将激活。
- **file：**最后，给定的文件名可以通过文件的existence或missing而激活profile。

activation元素不是profile可以被激活的唯一方式。setting.xml文件的activeProfile元素可能包含profile的id。它们也可以通过命令行在-P标志后用一个逗号分割的列表（如 -P test）来显示激活。   
要查看某个构建中将激活哪个配置文件，请使用maven-help-plugin。
```
mvn help:active-profiles
```
### Properties
Maven属性是值占位符，如Ant中的属性。它们的值可以通过使用符号$ {X}在POM中的任何位置访问，其中X是属性。他们有五种不同的样式，都可以从settings.xml文件访问：

1. env.X：使用“env.”替换变量将返回shell的环境变量。例如，$ {env.PATH}包含\ $ path环境变量（Windows中的％PATH％）。
2. project.x：POM中的点（.）标记路径将包含相应元素的值。例如：可以通过$ {project.version}访问&lt;project> &lt;version> 1.0 &lt;/ version> </ project>。
3. setting.x：settings.xml中的点（.）标记路径将包含相应元素的值。例如：可以通过 ${settings.offline}访问&lt;settings>&lt;offline>false&lt;/offline>&lt;/settings>。
4. Java系统属性：通过java.lang.System.getProperties()可访问的所有属性都可用作POM属性，例如$ {java.home}。
5. x：在<properties />元素或外部文件中设置，该值可以用作$ {someVar}。

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                      https://maven.apache.org/xsd/settings-1.0.0.xsd">
  ...
  <profiles>
    <profile>
      ...
      <properties>
        <user.install>${user.home}/our-project</user.install>
      </properties>
      ...
    </profile>
  </profiles>
  ...
</settings>
```
如果此配置文件处于活动状态，则可以从POM访问属性${user.install}。
### Repositories
Repositories是Maven用于填充构建系统的本地仓库的远程项目集合。从本地仓库中，Maven称它为插件和依赖。不同的远程仓库可能包含不同的项目，在开启的profile下，它们会搜索相匹配的发布或者快照版的artifact。
```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                      https://maven.apache.org/xsd/settings-1.0.0.xsd">
  ...
  <profiles>
    <profile>
      ...
      <repositories>
        <repository>
          <id>codehausSnapshots</id>
          <name>Codehaus Snapshots</name>
          <releases>
            <enabled>false</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
          </releases>
          <snapshots>
            <enabled>true</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
          </snapshots>
          <url>http://snapshots.maven.codehaus.org/maven2</url>
          <layout>default</layout>
        </repository>
      </repositories>
      <pluginRepositories>
        ...
      </pluginRepositories>
      ...
    </profile>
  </profiles>
  ...
</settings>
```
- **releases, snapshots:**这是对不同类型的artifact的策略，发布版或者快照版。使用这两个设置，POM有权在一个仓库中修改每个类型的策略，而不管其他的类型。比如，出于开发的目的，可能只决定开启快照版本下载。
- **enabled:**是否为相应的类型（发布版或快照版）启用了该存储库，为true还是false
- **updatePolicy:**此元素指定应尝试更新发生的频率。Maven将将本地POM的时间戳记（存储在仓库中的maven-metadata文件中）与远程文件进行比较。选项是：always，daily（默认），interval：X（其中X是以分钟为单位的整数）或never。
- **checksumPolicy:**当Maven将文件部署到仓库时，它还会部署相应的校验和文件。在文件缺失或者校验和不正确时，你的选项是ignore，fail或者warn。
- **layout:**在上述仓库的描述中，提到它们都遵循一个共同的布局。这大多数是正确的。 Maven 2为它的仓库有一个默认布局;然而，Maven 1.x有不同的布局。使用此元素来指定它是default还是legacy。


### 插件仓库
仓库是两种主要类型artifact的家。第一种类型的artifact用来做为其他artifact的依赖。这些是驻留在中央仓库的大多数插件。另一种类型的artifact是插件。Maven插件本身就是一种特殊类型的artifact。因此，插件仓库可能与其他仓库分离开来（尽管我还没有听到有说服力的论据）。无论如何，pluginRepositories元素块的结构类似于repository元素。pluginRepository元素各自指定Maven可以找到新插件的远程位置。

## Active Profiles
```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                      https://maven.apache.org/xsd/settings-1.0.0.xsd">
  ...
  <activeProfiles>
    <activeProfile>env-test</activeProfile>
  </activeProfiles>
</settings>
```
settings.xml难题的最后一块是activeProfiles元素。这包含一组activeProfile元素，每个元素都有一个profile id的值。任何profile id被定义为activeProfile都会被激活，而不管任何的环境设置。如果没有找到匹配的profile，则什么也不会发生。比如，如果env-test是一个activeProfile，一个在pom.xml（或profile.xml）中有相应id的profile会被激活。如果没有这样一个profile被找到，则依旧照常执行。