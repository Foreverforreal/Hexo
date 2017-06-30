title: java JDBC（二）使用JDBC处理SQL语句
id: 1498814336134
author: 不识
tags:
  - java
  - JDBC
categories:
  - java web
  - JDBC
date: 2017-06-30 17:18:00
---
# 建立连接
首先，你要与你要想要使用的数据源建立一个连接。数据源可以是一个DBMS，一个传统的文件系统，或者其他具有相应JDBC驱动的数据源。通常，一个JDBC应用程序使用以下两个类来与一个目标数据源建立连接。
- **DriverManager**：这个已经完全实现的类将应用程序连接到一个由数据库URL指定的数据源上。当这个类第一次尝试建立一个连接的时候，它会自动加载在class路径下的任何JDBC4.0驱动。需要注意的是，在4.0版本之前你的程序必须手动加载任何一个JDBC驱动。
- **Datesource**：这个接口优于DriverManager，因为它允许底层数据源的详细信息对你的程序透明。DataSource对象的属性设置为使其代表特定的数据源。更多信息参阅”使用Datasource对象连接“。有关使用DataSource类开发应用程序的更多信息，请参阅最新的Java EE教程。

<!-- more -->

> **注意**：本教程中的示例使用DriverManager类而不是DataSource类，因为它更易于使用，并且示例不需要DataSource类的功能。

## 使用DriverManager类
使用DriverManager类连接到您的DBMS涉及调用DriverManager.getConnection方法。以下JDBCTutorialUtilities.getConnection方法建立了一个数据库连接。
```java
public Connection getConnection() throws SQLException {

    Connection conn = null;
    Properties connectionProps = new Properties();
    connectionProps.put("user", this.userName);
    connectionProps.put("password", this.password);

    if (this.dbms.equals("mysql")) {
        conn = DriverManager.getConnection(
                   "jdbc:" + this.dbms + "://" +
                   this.serverName +
                   ":" + this.portNumber + "/",
                   connectionProps);
    } else if (this.dbms.equals("derby")) {
        conn = DriverManager.getConnection(
                   "jdbc:" + this.dbms + ":" +
                   this.dbName +
                   ";create=true",
                   connectionProps);
    }
    System.out.println("Connected to database");
    return conn;
}
```
DriverManager.getConnection方法用来建立一个数据库连接。这个方法需要一个数据库URL，它取决于您的DBMS。下面是一些数据库URL的示例。
1.	Mysql：jdbc:mysql://localhost:3306/，localhost是你的数据库的服务主机名称，3306是端口号
2.	Java DB：jdbc:derby:testdb;create=true,testdb]是要连接到的数据库的名字，而create = true则指示DBMS创建数据库。

	>**注意**：此URL与Java DB Embedded Driver建立数据库连接。Java DB还包括使用不同URL的Network Client Driver。
    
这个方法还需要指定用户名和密码，这里使用Properties对象用来访问DBMA。
**注意**
- 通常在数据库URL中，你还需要指定你想连接的一个已经存在的数据库名称，URL jdbc:mysql://localhost:3306/mysql表示一个名为mysql 的MySQL数据库的URL。本教程中的示例使用不指定特定数据库的URL，因为本示例想要创建一个新的数据库。
- 在之前的JDBC版本，要想获取一个连接，你首先必须通过调用Class.forName方法来初始化你的JDBC驱动。该方法需要一个java.sql.Driver类型的对象。每个JDBC驱动都包含一个或多个实现java.sql.Driver接口的类。Java DB的driver类是org.apache.derby.jdbc.EmbeddedDriver 以及org.apache.derby.jdbc.ClientDriver，而MySQL Connector / J中的一个是com.mysql.jdbc.Driver. 请参阅DBMS驱动程序的文档，以获取实现接口java.sql.Driver的类的名称。

	任何在class路径下找到的JDBC4.0的驱动都会被自动加载。（然而，你必须使用Class.forName方法手动加载任何早于JDBC4.0的驱动）
    
这个方法返回一个Connection对象，它代表一个同DBMS或者指定数据库的连接。通过这个对象来查询数据库。

## 指定数据库库连接URL
一个数据库连接URL是一个你的DBMS JDBC驱动用来连接数据库的字符串。它可能包含这些信息，比如去哪里查找数据库，要连接的数据库名称，以及配置属性。数据库连接URL的具体语法由你的DBMS来指定。
### Java DB 数据库连接URL
以下是Java DB的数据库连接URL语法：
```
jdbc:derby:[subsubprotocol:][databaseName]
    [;attribute=value]*
```
- subsubprotocol指定Java DB应在目录，内存，类路径或JAR文件中搜索数据库的位置。通常省略。
- databaseName是要连接的数据库的名称。
- attribute = value表示可选的以分号分隔的属性列表。这些属性使您能够指示Java DB执行各种任务，包括以下内容：
	- 创建连接URL中指定的数据库。 
	- 加密连接URL中指定的数据库。 
	- 指定目录以存储日志记录和跟踪信息。 
	- 指定连接到数据库的用户名和密码。


### MySQL Connector/J 数据库连接URL

以下是MySQL Connector / J的数据库连接URL语法：
```
jdbc:mysql://[host][,failoverhost...]
    [:port]/[database]
    [?propertyName1][=propertyValue1]
    [&propertyName2][=propertyValue2]...
```
- host:port是托管数据库的主机名和端口号。如果没有指定，host和port的默认值分别是127.0.0.1和3306。
- database是你要连接到的数据库的名称。如果没有指定，则连接不具有默认数据库。
- failover是备用数据库的名称（MySQL Connector / J支持故障切换）。
- propertyName = propertyValue表示一个可选的，和用用&符分隔的属性列表。这些属性使您能够指示MySQL Connector / J执行各种任务。

# 使用DataSource对象连接
　　本节涵盖了DataSource对象，它是连接到数据源的首选方式。除了它的会在后面介绍的优点之外，Datasource对象还可以提供连接池和分布式事务。此功能对企业数据库计算至关重要。特别地，它是Enterprise JavaBeans（EJB）技术的组成部分。     
　　本章节展示如何使用DataSource接口来获取一个连接，以及如何使用分布式事务与连接池。这两者在你的JDBC应用程序中只涉及很少的代码修改。     
　　系统管理员通常使用一个工具（比如Apache Tomcat 或 Oracle WebLogic Server）执行部署这些类来实现以上的功能操作，但这会根据正在部署的DataSource对象的类型而有所不同。因此，本节的大部分内容都致力于显示系统管理员如何设置环境，以便程序员可以使用DataSource对象获取连接。  

## 使用DataSource来获取一个连接
在"[建立连接](#建立连接)"中，你学会了如何使用DriverManager类来获取一个连接。这个章节将会为你展示如何使用DataSource对象来获取一个连接，到你的数据源中，这是一个更好的方式。    
由实现DataSource的类的实例化对象表示特定的DBMS或某些其他数据源，比如一个文件。一个DataSource对象表示一个特定的DBMS或者其他的数据源，比如一个文件。如果一个公司使用超过一个数据源的话，它会为其中的每个都部署一个单独的DataSource对象。DataSource接口由驱动程序供应商来实现。它可能以以下三种方式来实现。

- 一个基本的DataSource实现提供标准的Connection对象，这些对象不在连接池中或者在分布式事务中使用。
- 支持连接池的DataSource实现产生参与在连接池中的Connection对象，也就是说，连接可以被回收。
- 支持分布式事务的DataSource实现产生可在分布式事务中使用的Connection对象，也就是说，一个事务可以访问两个或多个DBMS服务器。

一个JDBC驱动应该至少包含一个基本的DataSource实现。比如，JavaDB JDBC驱动包含实现org.apache.derby.jdbc.ClientDataSource，并且对于MySQL，也包含一个DataSource实现com.mysql.jdbc.jdbc2.optional.MysqlDataSource. 如果您的客户端运行在Java 8 Compact Profile 2上，那么Java DB JDBC驱动程序就是org.apache.derby.jdbc.BasicClientDataSource40. 本教程的示例需要compact profile 3或更高版本。   
一个支持分布式事务的DataSource类通常也会实现对连接池的实现。例如，由EJB供应商提供的DataSource类几乎总是支持连接池和分布式事务。

在之前的例子中，假设The Coffee Break商店蓬勃发展的连锁店的所有者决定通过在互联网上销售咖啡来进一步扩张。随着预期的大量在线交易，所有者无疑需要一个连接池。连接的开启和关闭涉及大量开销，并且所有者预计这种在线订购系统将需要大量的查询和更新。通过连接池，池中的连接可以一次又一次地使用，从而避免为每次数据库访问来创建新连接的开销。此外，所有者现在有第二个DBMS,其中包含最近收购的咖啡焙烧公司的数据。这意味着所有者会希望能够编写同时使用旧的DBMS服务器和新的服务器的分布式事务。
连锁经营者重新配置了计算机系统，为新的更加广大的客户群服务。所有者已经购买了最新的JDBC驱动程序和一个EJB应用程序服务器，这可以用来做分布式事务，并且能够获得连接池带来的性能增长。许多JDBC驱动程序可用于与最近购买的EJB服务器兼容。所有者现在拥有了一个三层架构，一个新的EJB应用程序服务器和JDBC驱动在中间层，并且两个DBMS服务器作为第三层。发出请求的客户端计算机是第一层。

## 部署一个基础DataSource对象

系统管理员需要部署DataSource对象，这样The Coffee Break的编程团队可以开始使用它们。部署一个DataSource对象包含以下三个任务。
1.	创建一个DataSource类的实例
2.	设置它的属性。
3.	使用Java命名和目录接口（JNDI）API的命名服务注册它。

首先，考虑使用一个DataSource接口基本实现的最基础情况，也就是一个不支持连接池或分布式事务的DataSource。这种情况下只有一个DataSource对象需要部署。一个DataSource的基础实现提供与DriverManager类提供的相同种类的连接。

### 创建DataSource类实例并设置它的属性

假设一个公司只想要一个DataSource的基础实现，它已经从JDBC供应商DB Access Inc购买了一个驱动。这个驱动包含了com.dbaccess.BasicDataSource类，它实现了DataSource接口。下面的代码片段创建了一个BasicDataSource类的实例，并且设置了它的属性。在BasicDataSource的实例部署后，程序员可以调用DataSource.getConnection方法来获取一个到公司数据库CUSTOMER\_ACCOUNTS的连接。首先系统管理员使用默认构造器创建了BasicDataSource对象ds。然后系统管理员设置了三个属性。注意，以下代码通常由部署工具执行。
```java
com.dbaccess.BasicDataSource ds = new com.dbaccess.BasicDataSource();
ds.setServerName("grinder");
ds.setDatabaseName("CUSTOMER_ACCOUNTS");
ds.setDescription("Customer accounts database for billing");
```
变量ds现在表示托管在在服务器上的数据库CUSTOMER\_ACCOUNTS。任何由BasicDataSource对象ds产生的连接都是到数据库CUSTOMER\_ACCOUNTS的连接。

### 使用JNDI命名服务注册DataSource对象

通过设置属性，系统管理员可以使用JNDI（Java命名和目录接口）命名服务注册BasicDataSource对象。所使用的命名服务通常由系统属性确定，这里不展示。下面代码片段注册BasicDataSource对象并且将它与逻辑名称jdbc/billingDB绑定在一起
```java
Context ctx = new InitialContext();
ctx.bind("jdbc/billingDB", ds);
```
这里的代码使用了JNDI API。第一行创建了一个InitialContext对象，服务器把它作为命名起点，类似于一个文件系统的根目录。第二行将BasicDataSource对象ds关联或者说绑定到逻辑名jdbc/billingDB上。在下个代码片段，你传递给命名服务这个逻辑名，然后它返回给你BasicDataSource对象。逻辑名可以是任何字符串。在这种情况下，公司决定使用名称billingDB作为CUSTOMER
\_ACCOUNTS数据库的逻辑名称。      
在前面的示例中，jdbc是初始上下文中的一个子上下文，就像在根目录下的目录是一个子目录一样。Jdbc/billingDB就像一个路径名称，路径中最后一个项目类似于文件名。在这种情况下，billingDB是赋予BasicDataSource对象ds的逻辑名称。子上下文jdbc保留用于绑定到DataSource对象的逻辑名，因此jdbc将始终是数据源的逻辑名的第一部分。

### 使用部署的DataSource对象
在一个基础DataSource实现被系统管理员部署后，它已经可以被程序员所使用。这意味着程序员可以给出一个被绑定到DataSource类实例上的逻辑数据源名称，然JNDI命名服务会返回一个DataSource类的实例。DataSource对象上的getConnection方法可以被调用，用来获取一个到它所表示的数据源上的连接。比如，一个程序员可以编写以下两行代码来获取一个DataSource对象，它产生一个到数据库CUSTOMER\_ACCOUNTS的连接。
```java
Context ctx = new InitialContext();
DataSource ds = (DataSource)ctx.lookup("jdbc/billingDB");
```
第一行代码获取一个初始上下文作为接收DataSource对象的起点。当你提供这个逻辑名jdbc/billingDB给方法lookup，这个方法返回被系统管理员部署时绑定到jdbc/billingDB上的DataSource对象。因为lookup方法的返回值是一个Java Object类型，所以在将它赋给变量ds前，我们必须将它转换为更为具体的DataSource类型。   
变量ds是实现了DataSource接口的类com.dbaccess.BasicDataSource的实例。调用方法ds.getConnection产生一个到数据库CUSTOMER\_ACCOUNTS的连接。     
```java
Connection con = ds.getConnection("fernanda","brewed");
```
getConnection方法只需要用户名和密码，因为在变量ds的属性中有与数据库CUSTOMER\_ACCOUNTS建立连接所需的其他信息，比如数据库名称和位置。


### DataSource对象的优点
由于它能设置属性，与获取连接的DriverManager类相比，DataSource对象是一个更好的选择。程序员不再需要在其应用程序中硬编码驱动程序名称或JDBC URL，这使得它们更易移植。此外，DataSource的属性使维护代码更简单。如果有更改，系统管理员可以更新数据源属性，而不用担心更改与数据源建立连接的每个应用程序。例如，如果将数据源移动到其他服务器，所有系统管理员需要做的是设置serverName属性为新的服务器名称。     
除了可移植性和易于维护之外，使用DataSource对象获取连接还可以提供其他优势。当DataSource接口实现为使用ConnectionPoolDataSource实现时，由该DataSource类的实例生成的所有连接将自动成为池连接。类似地，当DataSource的实现也是使用XADataSource的实现时，它产生的所有连接将自动成为可以在分布式事务中使用的连接。下一节将介绍如何部署这些类型的DataSource实现。    

## 部署其他DataSource实现

系统管理员或相同身份的人可以部署DataSource对象，使其生成的连接是池连接。要想做到这一点，他或她首先部署一个ConnectionPoolDataSource对象，然后部署一个DataSource对象，实现和它一起工作。设置ConnectionPoolDataSource对象的属性，以便它表示要生成连接的数据源。在ConnectionPoolDataSource对象已经使用JNDI命名服务注册之后，DataSource对象被部署。通常只需要为DataSource对象设置两个属性：description和dataSourceName。赋给dataSourceName属性的值是标识先前部署的ConnectionPoolDataSource对象的逻辑名称，该ConnectionPoolDataSource对象包含进行连接所需的属性。     
在ConnectionPoolDataSource和DataSource对象部署后，你可以调用DataSource对象上的方法DataSource.getConnection来获取池连接。此连接将连接到ConnectionPoolDataSource对象的属性中指定的数据源。     
以下示例描述了The Coffee Break的系统管理员如何部署实现的提供池连接的DataSource对象。系统管理员通常将使用部署工具，因此本节中显示的代码片段是部署工具将执行的代码。
为了获得更好的性能，Coffee Break公司已经从DB Access，Inc.购买了一个JDBC驱动程序，其中包含实现ConnectionPoolDataSource接口的com.dbaccess.ConnectionPoolDS类。系统管理员创建创建此类的实例，设置其属性，并使用JNDI命名服务进行注册。Coffee Break从其EJB服务器供应商Application Logic公司购买了DataSource类的实现 com.applogic.PooledDataSource。com.applogic.PooledDataSource类通过使用ConnectionPoolDataSource类com.dbaccess.ConnectionPoolDS提供的底层支持来实现连接池。


ConnectionPoolDataSource对象必须首先被部署。下面代码创建了一个com.dbaceccesss.ConnectionPoolDS类的实例并且设置了它的属性。
```java
com.dbaccess.ConnectionPoolDS cpds = new com.dbaccess.ConnectionPoolDS();
cpds.setServerName("creamer");
cpds.setDatabaseName("COFFEEBREAK");
cpds.setPortNumber(9040);
cpds.setDescription("Connection pooling for " + "COFFEEBREAK DBMS");
```
在ConnectionPoolDataSource对象被部署后，系统管理员部署DataSource对象。下面的代码使用JNDI命名服务来注册com.dbaccess.ConnectionPoolDS对象cpds。注意，与变量cpds相关联的逻辑名让它的子上下文pool添加到了子上下文jdbc下面，这类似于在一个分层文件系统中添加一个子目录到另一个子目录下。com.dbaccess.ConnectionPoolDS类的任何实例的逻辑名会一直以jdbc/pool开头。Oracle建议将所有ConnectionPoolDataSource对象放在子上下文jdbc / pool下：
```java
Context ctx = new InitialContext();
ctx.bind("jdbc/pool/fastCoffeeDB", cpds);
```
接下来，一个被实现与cpds以及其他com.dbaccess.ConnectionPoolDS类实例交互的DataSource对象被部署。以下代码创建此类的实例并设置其属性。注意，com.applogic.PooledDataSource的实例只设置了两个属性。Description属性被设置，是因为它一直都是需要的。另一个被设置的属性是dataSourceName，它为com.dbaccess.ConnectionPoolDS 类的实例cpds提供一个逻辑JNDI名称。换句话说，cpds表示为DataSource对象实现连接池的ConnectionPoolDataSource对象。 

下面的代码可能会由一个部署工具来执行，创建一个PooledDataSource对象，设置它的属性，并且将它绑定在逻辑名jdbc/fastCoffeeDB上。
```java
com.applogic.PooledDataSource ds = new com.applogic.PooledDataSource();
ds.setDescription("produces pooled connections to COFFEEBREAK");
ds.setDataSourceName("jdbc/pool/fastCoffeeDB");
Context ctx = new InitialContext();
ctx.bind("jdbc/fastCoffeeDB", ds);
```
此时，部署了一个DataSource对象，应用程序可以从该对象获取到数据库COFFEEBREAK的池连接。

## 获取和使用池连接
连接池是一个数据库连接对象的缓存。这些对象表示物理的数据库连接，可以由应用程序用于连接到数据库。在运行时期，应用程序从池中请求获取一个连接。如果池内包含可以满足请求的连接，则返回给应用程序这个连接。如果池内没有连接，则会创建一个新的连接并返回给应用程序。应用程序使用连接来在数据库上执行一些操作，然后返回连接对象给连接池。这样连接可用于下一个连接请求。     
连接池促进连接对象的重用，并减少创建连接对象的次数。连接池显着提高了数据库密集型应用程序的性能，因为创建连接对象在时间和资源方面都是昂贵的。    
现在DataSource和ConnectionPoolDataSource对象都已经被部署，程序员可以使用DataSource对象获得一个池连接。获取池连接的代码就像获取非池连接的代码一样，如以下两行所示：   
```java
ctx = new InitialContext();
ds = (DataSource)ctx.lookup("jdbc/fastCoffeeDB");
```
变量ds表示DataSource对象，它产生与数据库COFFEEBREAK的池连接。你只需要获取这个DataSource对象一次，因为你可以使用它按照需要产生许多池连接。在ds变量上调用getConnection方法会自动生成一个池连接，因为ds变量所表示的DataSource对象被配置为产生池连接。     
连接池通常对程序员是透明的。使用池化连接时，只需要做两件事情：    
1.	使用DataSource对象而不是DriverMananger类来获取一个连接。在下面一行代码中，ds是实现和部署的DataSource对象，因此它将创建池连接，username和password是表示可以访问数据库的用户凭据的变量：  
```
Connection con = ds.getConnection(username, password);
```
2.	使用finally语句来关闭一个池连接。下面的finally块出现在应用于使用池连接的代码的try / catch块之后：
```java
try {
    Connection con = ds.getConnection(username, password);
    // ... code to use the pooled
    // connection con
} catch (Exception ex {
    // ... code to handle exceptions
} finally {
    if (con != null) con.close();
}
```

除此之外，使用池连接的应用程序与使用常规连接的应用程序相同。程序员可能唯一注意到的一件事是，当连接池使用后性能会更好。   
以下示例代码获取一个DataSource对象，该对象生成与数据库COFFEEBREAK的连接，并使用它来更新表COFFEES中的价格：  
```java
import java.sql.*;
import javax.sql.*;
import javax.ejb.*;
import javax.naming.*;

public class ConnectionPoolingBean implements SessionBean {

    // ...

    public void ejbCreate() throws CreateException {
        ctx = new InitialContext();
        ds = (DataSource)ctx.lookup("jdbc/fastCoffeeDB");
    }

    public void updatePrice(float price, String cofName,
                            String username, String password)
        throws SQLException{

        Connection con;
        PreparedStatement pstmt;
        try {
            con = ds.getConnection(username, password);
            con.setAutoCommit(false);
            pstmt = con.prepareStatement("UPDATE COFFEES " +
                        "SET PRICE = ? " +
                        "WHERE COF_NAME = ?");
            pstmt.setFloat(1, price);
            pstmt.setString(2, cofName);
            pstmt.executeUpdate();

            con.commit();
            pstmt.close();

        } finally {
            if (con != null) con.close();
        }
    }

    private DataSource ds = null;
    private Context ctx = null;
}
```

此代码示例中的连接参与连接池，因为：
- 一个实现ConnectionPoolDataSource的类的实例已经被部署。
- 一个实现DataSource的类的实例已经被部署，并且设置了它的dataSource属性值为我们绑定到之前部署的ConnectionPoolDataSource对象的逻辑名。

请注意，虽然这段代码与以前见过的代码非常相似，但它在以下方面是不同的：
- 除了java.sql外，它还导入了javax.sql,javax.ejb以及javax.naming包。

	DataSource和ConnectionPoolDataSource接口在javax.sql包中，而JNDI构造器InitialContext和Context.lookup方法是javax.naming包中的一部分。该特定示例代码采用EJB组件的形式，该组件使用javax.ejb包中的API。此示例的目的是显示你使用池连接的方式与使用非池连接的相同，因此您不用担心不了解EJB API。
- 它使用DataSource对象来获取连接，而不是使用DriverManager。
- 它使用finally块来确保连接已关闭。

获取和使用池化连接与获取和使用常规连接类似。当作为系统管理员的人员正确部署了一个ConnectionPoolDataSource对象和一个DataSource对象时，应用程序使用该DataSource对象来获取一个池化的连接。应用程序应该使用finally块来关闭池化连接。为了简单起见，前面的例子使用了一个finally块，但没有catch块。如果try块中的方法抛出异常，它会依照默认抛出异常，而finally子句将在任何情况下执行。   

## 部署分布式事务
