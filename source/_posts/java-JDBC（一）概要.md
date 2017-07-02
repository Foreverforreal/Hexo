title: java JDBC（一）概要
id: 1498812139937
author: 不识
tags:
  - java
  - JDBC
categories:
  - java 基础
  - JDBC
date: 2017-06-30 16:42:00
---
# JDBC 介绍
JDBC API是一种可以访问任何类型表格数据，特别是存储在关系数据库中的数据的Java API。
JDBC帮你编写一个Java应用程序，用来管理以下三种编程活动。
1.	连接数据源比如一个数据库。
2.	向数据库发送查询和更新语句。
3.	接收并处理从数据库收到的作为对你查询回应的结果。

<!-- more -->

以下简单的代码片段给出了以上三个步骤的简单示例：
```java
public void connectToAndQueryDatabase(String username, String password) {

    Connection con = DriverManager.getConnection(
                         "jdbc:myDriver:myDatabase",
                         username,
                         password);

    Statement stmt = con.createStatement();
    ResultSet rs = stmt.executeQuery("SELECT a, b, c FROM Table1");

    while (rs.next()) {
        int x = rs.getInt("a");
        String s = rs.getString("b");
        float f = rs.getFloat("c");
    }
}
```
这个代码片段实例化一个DriverManager对象来连接到数据库驱动并登录数据库，它实例化一个Statement对象，将SQL查询语句传递给数据库。实例化一个ResultSet对象，用来获取你的查询结果，并且执行一个简单的while循环，该循环获取并显示这些结果。

## JDBC产品组件
JDBC包括四个组件：
1. **The JDBC API** —JDBC™API提供了从Java™编程语言到关系型数据的编程访问。使用JDBC API，应用程序可以执行SQL语句，获取结果，并将更改传播到底层数据源。JDBC API还可以在分布式异构环境中与多个数据源进行交互。 

	JDBC API是Java平台的一部分，它包括Java™标准版（Java™SE）和Java™企业版（Java™EE）。JDBC 4.0 API分为两个包：java.sql和javax.sql。这两个包都包含在Java SE和Java EE平台中。
2. **JDBC** Driver Manager —JDBC DriverManager类用来确定可将Java应用程序连接到JDBC驱动程序的对象。在传统上DriverManager已经是JDBC架构的骨干。它相当小而简单。    

	标准扩展包javax.naming 和javax.sql允许你用Java命名和目录接口（Java Naming and Directory Interface）™（JNDI）的命名服务注册一个DataSource对象，使用用它来建立与数据源的连接。您可以使用其他的连接机制，但建议尽可能使用DataSource对象。 
3. **JDBC Test Suite** —  JDBC驱动测试套件可帮助您确定JDBC驱动将运行您的程序。这些测试并不全面和具体，但它们检测了许多JDBC API的重要功能。
4. **JDBC-ODBC Bridge** —Java Software桥接器通过ODBC驱动程序提供JDBC访问。请注意，您需要将ODBC二进制代码加载到使用此驱动程序的每个客户端计算机上。因此，ODBC驱动程序最适合在一个公司网络，这样客户端安装不是主要问题，或者是在一个在三层架构中服务端代码是用Java编写的应用程序上。

本教程使用这四个JDBC组件的前两个连接到数据库，然后构建一个使用SQL命令与测试关系型数据库进行通信的java程序。最后两个组件用于专门的环境中以测试Web应用程序，或与ODBC-aware DBMS进行通信。

## JDBC架构
JDBC API支持用来数据库访问的两层和三层处理模型。
![数据访问两层模型](/images/javaweb/intro.anc2.gif)
在两层模型中，Java applet或应用程序直接与数据源进行通信。这需要一个能够与被访问的特定数据源通信的JDBC驱动。它将用户的命令传递给数据库或其他数据源，并将这些语句的结果发送回用户。数据源可能部署在其他的机器上，用户需要通过网络进行连接。这被称为客户端/服务器配置，用户的机器是客户端，而托管数据源的机器是服务器。网络可以是内联网，例如在公司内部将员工连接起来，也可以是互联网。    

在三层模型中，命令被发送给服务的中间层，然后它再将命令发给数据源。数据源处理命令并且将结果发回中间层，然后中间层再将结果发给用户。在管理信息系统中三层模型非常有吸引力，因为中间层可以保持对访问的控制以及对企业数据可以进行的更新种类。另一个优点是它简化了应用程序的部署。最后，在许多情景下，三层架构可以提供更好的性能优势。
![数据访问三层模型](/images/javaweb/intro.anc1.gif)
直到最近，中间层还经常用C或C ++等语言编写，这提供了更快的性能。然而，随着能将Java字节码转换为高效的机器特定代码的编译器的优化，以及一些技术比如Enterprise JavaBeans™的引进，Java平台正在迅速成为中间层开发的标准平台。这是一个很大的加分，使它可以利用Java的强大，多线程和安全功能。
随着使用Java编程语言来编写服务端代码的企业的增长，JDBC API正在三层架构的中间层中被越来越多地使用。JDBC的支持连接池，分布式事务以及disconnected rowsets这些功能使其成为了一项服务器技术。JDBC API同样也允许从Java中间层访问数据源。

# 使用JDBC处理SQL语句
通常，要使用JDBC处理任何SQL语句，请按照下列步骤操作：
1.	建立连接。
2.	创建statement
3.	执行查询
4.	处理ResultSet对象
5.	关闭连接

这节使用下面的CoffeesTables.viewTable方法来演示这些步骤。此方法输出COFFEES表里的内容。在后面的章节中会讨论该方法更多细节。
```java
public static void viewTable(Connection con, String dbName)
    throws SQLException {

    Statement stmt = null;
    String query = "select COF_NAME, SUP_ID, PRICE, " +
                   "SALES, TOTAL " +
                   "from " + dbName + ".COFFEES";
    try {
        stmt = con.createStatement();
        ResultSet rs = stmt.executeQuery(query);
        while (rs.next()) {
            String coffeeName = rs.getString("COF_NAME");
            int supplierID = rs.getInt("SUP_ID");
            float price = rs.getFloat("PRICE");
            int sales = rs.getInt("SALES");
            int total = rs.getInt("TOTAL");
            System.out.println(coffeeName + "\t" + supplierID +
                               "\t" + price + "\t" + sales +
                               "\t" + total);
        }
    } catch (SQLException e ) {
        JDBCTutorialUtilities.printSQLException(e);
    } finally {
        if (stmt != null) { stmt.close(); }
    }
}
```
## 建立连接

首先与你要使用的数据源建立一个连接。数据源可以是一个DBMS，一个传统的文件系统，或者其他具有相应JDBC驱动的数据源。连接由Connection对象表示。更多信息参阅“建立连接”中。
## 创建Statement
Statement是一个代表一个SQL语句的接口。你执行Statement对象，并生成ResultSet对象，它是一个表示数据库结果集的数据表。要创建Statament对象，你需要一个Connection对象。    
比如，CoffeesTables.viewTable用以下代码创建了一个Statement对象。
```java
stmt = con.createStatement();
```
有以下三种形式的statement：
- **Statement**：用来实现简单的不带参数的SQL语句
- **PrepareStatement**：（继承自Statement）用来预编译可能包含输入参数的SQL语句。更多信息参阅“使用预编译语句”。
- **CallableStatement**：（继承自PreparedStatement）用来执行可能同时包含输入输出参数的存储过程。更多信息参阅“存储过程”

## 执行查询

要执行查询操作，调用Statement中的execute方法，如下所示

- **execute**：如果查询返回的第一个对象是ResuleSet对象的话，则返回true。如果查询可能返回一个或多个ResultSet对象的话使用这个方法。可以通过反复调用Statement.getResultSet方法，获取查询返回的ResulteSet对象。
- **executeQuery**：返回一个Resultset对象。
- **executeUpdate**：返回一个表示受SQL语句影响行数的整数值。当执行INSERT,DELETE,或者UPDATE这些SQL语句时，使用该方法。
例如，CoffeesTables.viewTable使用以下代码执行Statement对象：
```java
ResultSet rs = stmt.executeQuery(query);
```
更多信息参阅“从ResulteSet中获取和修改值”

## 处理ResultSet对象

比如CoffeesTables.viewTable方法反复调用ResultSet.next来将游标向前移动一行。每次调用next，这个方法都输出一个游标目前所在行的数据。
```java
try {
    stmt = con.createStatement();
    ResultSet rs = stmt.executeQuery(query);
    while (rs.next()) {
        String coffeeName = rs.getString("COF_NAME");
        int supplierID = rs.getInt("SUP_ID");
        float price = rs.getFloat("PRICE");
        int sales = rs.getInt("SALES");
        int total = rs.getInt("TOTAL");
        System.out.println(coffeeName + "\t" + supplierID +
                           "\t" + price + "\t" + sales +
                           "\t" + total);
    }
}
// ...
```
更多信息参阅“从ResulteSet中获取和修改值”

## 关闭连接

当你结束使用Statement时，调用Statement.close方法来立即释放它使用的资源。当你调用这个方法时，它的ResultSet对象也被关闭。
比如，CoffeesTables.viewTable方法通过使用finally块包裹执行关闭的代码，来确保Statement对象在方法结束时被关闭，无论过程中是否有任何一个SQLException被抛出，
```java
} finally {
    if (stmt != null) { stmt.close(); }
}
```
当与数据源交互遇到错误的时候，JDBC会抛出一个SQLException异常。更多信息参阅“处理SQL异常”
在Java SE 7或更高版本可用的JDBC4.1中，你可以使用try-with-resources语句来自动关闭Connection，Statement以及ResultSet对象，无论是否有SQLException抛出。一个自动关闭资源语句由一个try语句以及一个或多个声明的资源组成。比如，比可以修改CoffeesTables.viewTable方法使它的Statement对象可以自动关闭，如下所示。
```java
public static void viewTable(Connection con) throws SQLException {

    String query = "select COF_NAME, SUP_ID, PRICE, " +
                   "SALES, TOTAL " +
                   "from COFFEES";

    try (Statement stmt = con.createStatement()) {

        ResultSet rs = stmt.executeQuery(query);

        while (rs.next()) {
            String coffeeName = rs.getString("COF_NAME");
            int supplierID = rs.getInt("SUP_ID");
            float price = rs.getFloat("PRICE");
            int sales = rs.getInt("SALES");
            int total = rs.getInt("TOTAL");
            System.out.println(coffeeName + ", " + supplierID +
                               ", " + price + ", " + sales +
                               ", " + total);
        }
    } catch (SQLException e) {
        JDBCTutorialUtilities.printSQLException(e);
    }
}
```
下面是一个try-with-resources语句，它声明了一个资源stmt，当try块终止时，它将自动关闭：
```java
try (Statement stmt = con.createStatement()) {
    // ...
}
```