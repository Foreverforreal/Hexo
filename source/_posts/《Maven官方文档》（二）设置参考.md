title: 《Maven官方文档》（二）设置参考
id: 1499051073985
author: 不识
tags:
  - Maven
  - 构建工具
categories:
  - Maven
date: 2017-07-03 11:04:00
---
# 简介
## 快速概览
setting.xml文件中的setting元素包含了用于定义以各种方式配置Maven执行的值的元素，比如pom.xml，但是不应该捆绑到任何具体的项目或分发给用户。它包含一些值，比如本地仓库的位置，可选的远程仓库服务器，以及验证信息。    
setting.xml文件可能存在于两个位置：
1. Maven安装目录下：$ {maven.home} /conf/settings.xml
2. 用户安装目录： ${user.home}/.m2/settings.xml

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

注意，setting.xml中定义在profiles中的属性不可以用来插入值。

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
- **userPluginRegistry**：如果Maven应该使用 ${user.home}/.m2/plugin-registry.xml文件来管理插件版本，则为true。默认为false。注意当前的Maven 2.0版本，不应该依赖于plugin-registry.xml文件。目前认为它无效。
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
用于下载和部署的仓库由POM的repositories和distributionManagement元素定义。但是，某些设置（如用户名和密码）不应与pom.xml一起分发。这种类型的信息应该存在于settings.xml中的构建服务器上。
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
- **id** 这是服务器的ID（不是用户用来登录的）
- ****
- ****
- ****
- ****
### 密码加密
## 镜像
## 代理
## Profiles
###