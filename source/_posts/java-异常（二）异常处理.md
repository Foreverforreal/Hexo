title: java 异常（二）异常处理
id: 1495939244594
author: 不识
date: 2017-05-28 10:40:58
tags:
---
# 捕获异常
捕获异常有三个组件——try,catch和finally代码块。在Java SE 7后新引进了try-with-resources语句。ry-with-resources语句特别适用于使用可关闭资源（如流）的情况。

## try代码块

构造异常处理程序的第一步是使用try代码块将可能抛出异常的代码括起来。通常try代码块看起来如下面一样
```java
	try{
		code
	}
```
<!-- more -->
用code标记的部分包含一行或多行可能抛出异常的合法代码。如果try块中发生异常，该异常由与其关联的异常处理程序处理。要将异常处理程序与try块相关联，您必须在其后面加上一个catch代码块;
## catch代码块
通过在try代码块之后直接提供一个或多个catch代码块，将异常处理程序与try块相关联。try代码块的末尾和第一个catch代码块的开始之间没有代码。
```java
try {

} catch (ExceptionType name) {

} catch (ExceptionType name) {

}
```
每个catch块都是一个异常处理程序，用于处理其参数标示的异常类型。这个类型参数ExceptionType声明了处理程序可以处理的异常类型，并且它必须是继承自Throwable。

catch块包含在异常处理程序被调用时和执行时执行的代码。当调用栈抛出一个异常时，系统会匹配与该异常相符的ExceptionType的catch代码块,然后执行其内的代码，用来打印错误、停止程序，或者其他更多操作，比如进行错误恢复，提示用户做出决定，或者使用链接异常将错误传播到更高级别的处理程序。

在Java 7以及之后版本，单个catch块可以处理多种类型的异常，在catch后小括号中，可以指定多个处理的异常类型，并使用垂直条（|）分隔每个异常类型：
```java
catch (ExceptionType | ExceptionType ex) {
 	code
}

```
>如果一个catch块处理多个异常类型，那么catch参数是隐式的final。在这个例子中，catch参数ex是final，因此你不能在catch块内给它赋值。

## finally代码块

finally代码块总是在try代码块退出时执行。这确保即使发生意外异常也会执行finally。但是finally不仅是用来做异常处理，它还允许程序员由于意外的绕过了return,continue或break时做代码清除工作。将清理代码放在finally块中始终是一个很好的做法，甚至即使没有预期到异常。
>如果在执行try或catch代码时JVM退出，那么finally块可能不会执行。同样，如果执行try或catch代码的线程被中断或者被杀死，即使应用程序整体上继续，finally块也可能不会执行。

在使用I/O流时，应当确保最终的流的关闭。使用finally
```java
  InputStream input = null;

    try {
        input = new FileInputStream("file.txt");

        int data = input.read();
        while(data != -1){
            System.out.print((char) data);
            data = input.read();
        }
    } finally {
        if(input != null){
            input.close();
        }
    }
}

```

finally块是防止资源泄漏的关键工具。关闭文件或以其他方式恢复资源时，请将代码放在finally块中，以确保资源最终能够被释放。但是这种情况应当考虑使用try-with-resources语句，在不再需要时会自动释放系统资源。

## try-with-resource语句
try-with-resources语句是在一个try语句中声明一个或多个资源。一个资源是程序完成后必须关闭的对象。try-with-resources语句确保每个资源在语句结尾处被关闭。实现java.lang.AutoCloseable的任何对象（包括实现java.io.Closeable的所有对象）都可以被看作一个资源使用。

```java
static String readFirstLineFromFile(String path) throws IOException {
    try (BufferedReader br = new BufferedReader(new FileReader(path))) {
        return br.readLine();
    }
}

```
在此示例中，在try-with-resources语句中声明的资源是BufferedReader。BufferedReader在Java 7之后实现了java.lang.AutoCloseable接口由于它被声明在try-with-resource语句中，所以不管try语句是正常结束还是突然结束（由于方法BufferedReader.readLine抛出IOException），它都会被最终关闭。

try-with-resources语句也可以包含多个资源声明，它们之间使用分号分割。并且在语句结束时，都会调用它们的close方法来关闭资源。需要注意的是，资源的close方法是与资源创建的相反顺序进行调用的。

一个try-with-resources语句同样可以有一个catch代码块和finally代码块，就如普通的try语句一样。在一个try-with-resources语句中，任何catch或finally块都在关闭资源声明之后运行。比如下面使用try-with-resources语句自动关闭java.sql.Statement对象：
```java
public static void viewTable(Connection con) throws SQLException {

    String query = "select COF_NAME, SUP_ID, PRICE, SALES, TOTAL from COFFEES";

    try (Statement stmt = con.createStatement()) {
        ResultSet rs = stmt.executeQuery(query);

        while (rs.next()) {
            String coffeeName = rs.getString("COF_NAME");
            int supplierID = rs.getInt("SUP_ID");
            float price = rs.getFloat("PRICE");
            int sales = rs.getInt("SALES");
            int total = rs.getInt("TOTAL");

            System.out.println(coffeeName + ", " + supplierID + ", " + 
                               price + ", " + sales + ", " + total);
        }
    } catch (SQLException e) {
        JDBCTutorialUtilities.printSQLException(e);
    }
}

```

# 抛出异常

在异常的处理上，有时我们不需要在方法产生异常的地方进行处理，而是进一步由调用栈来处理该异常。这时候不必再使用try catch代码块来捕获异常，而是在方法体上使用throws抛出可能出现的异常，由调用该方法的部分来进行异常处理。这种异常处理方法写法如下

```java

public void method() throws IOException {

}
```
throws抛出的异常可以是一个或多个，多个异常时使用逗号来分割开。