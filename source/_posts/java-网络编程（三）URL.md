title: java 网络编程（三）URL
id: 1495121454250
author: 不识
tags:
  - java
categories:
  - java 基础
  - java 网络编程
date: 2017-05-18 23:30:00
---
URL是统一资源定位符的缩写，用来在网络中定位获取资源。java.net包中提供一个URL类来代表一个URL地址。
尽管不确切，但是通常我们认为一个URL就是一个在万维网上的文件名，因为大多数的URL就是指一个在计算机或网络上的文件。但是需要注意的是URL同样可以指向网络上的其他资源，比如数据库查询和命令输出。

<!-- more -->

一个URL有两个主要组成部分

- 协议标识符：对于URL http ://example.com来说，http就是协议标识符
- 资源名：对于URL http: //example.com来说，example.com就是资源名

需要注意的是协议标识符与资源名之间通过“://”来分割，协议标识符表示用来获取资源的协议名称，例子中使用的是超文本传输协议（HTTP）,通常用于提供超文本文档。资源名是资源的完整地址。资源名的格式完全依赖它所使用的协议，但是对于大多数协议来说，包括HTTP,资源命名通常包括一下一个或多个组成。

- **主机名**（Host Name） 
资源所存在的机器名称
- **文件名**（File Name）
机器中文件路径地址
- **端口号**（Port Number）
需要连接的端口号（通常可选）
- **引用**（Reference）
资源中通常标识文件中特定位置（通常为可选）的命名锚点的引用。

对于大多数协议来说，主机名和文件名都是必须的，而端口号和引用则是可选的。例如HTTP URL的资源名称必须指定网络上服务器（主机名）和该机器上的文档路径（文件名）

***
# 创建URL
***

## 常用构造方法

通常我们使用一个绝对路径创建一个URL,比如
```java
URL url = new URL("http://example.com/");
```
但也可是使用相对路径的方式创建一个URL
```java
URL baseUrl = new URL("http://example.com/");

URL url1 = new URL(baseUrl,"page1.html");
URL url2 = new URL(baseUrl,"page2.html");
```
这种方式需要另一个URL对象来作为基础URL,后面一个String参数指定在这个URL基础上的资源名，这很适合一些用锚点命名的URL,比如
```java
URL page1BottomURL = new URL(page1URL,"#BOTTOM");
```
除了这些外，URL还提供了额外的两个构造方法，可以让分别传入URL的组成部分，协议名、主机名、端口号、和文件名

```java
URL(String protocol, String host, int port, String file)

URL(String protocol, String host, String file)

```
当我们创建一个URL后，如果想要获得该URL的完整地址字符串，可以使用URL.toString()或URL.toExternalForm()方法，这两个是等效的。

>- 当URL构造方法给定的参数为null，或者协议名是一个未知的协议，就会抛出MalformedURLException 异常。  
- URL还是一个一次性写入的对象，一旦我们创建该对象后，它的任何属性都无法再修改（协议，主机名，文件名，或端口号）

## URL内特殊字符
有些URL地址里含有一些特殊的字符，我们需要对其进行编码，比如
>http ://example.com/hello world/

在创建URL时就需要特殊处理
```java
URL url = new URL("http://example.com/hello%20world");

```
但是当特殊字符比较多，或者我们不确定要访问的URL地址时，可以使用java.net.URI的多参构造创建一个URI对象，然后再调用它的方法转换为URL，这是java会帮我们自动进行编码工作，如下
```java
URI uri = new URI("http", "example.com", "/hello world/", "");

URL url = uri.toURL();

```
我们也可以使用专门的工具类URLEncode来对URL路径进行编码，URLEncode有两个静态方法encode(String s)和encode(String s,String enc)前一个方法已经不推荐使用，后一个需要我们指定编码的字符集，该方法会将url中的特殊字符按照给定字符集编码成“%XX”形式的十六进制表示，示例代码如下
```java
URL url1 = new URL("http","example.com/",URLEncoder.encode("hello world","utf-8"));
```
>需要注意的是URLEncode.encode会把“/”当作特殊字符进行编码，所以只能用来处理url中后缀部分。

***
# 解析URL
***
URL类提供了一系列的方法用来查询URL对象的属性，如下表

|方法名|作用|
|------|----|
|**getProtocol()**|返回URL的协议标识符|
|**getAuthority()**|返回URL的权限组件|
|**getHost()**|返回URL的主机名组件|
|**getPort()**|返回URL的端口号组件|
|**getPath()**|返回URL的路径组件|
|**getQuery()**|返回URL的查询组件|
|**getFile()**|返回URL的文件名，它相当于getPath()加上getQuery()|
|**getRef()**|返回URL的引用组件|

>需要注意的是不是每个URL都包含以上的组件，URL提供这些方法，只是因为HTTP URL包含这些组件。虽然URL支持各种协议，但是有点是以HTTP协议为中心设计的。

下面是示例代码

```java
public class ParseURL {
    public static void main(String[] args) throws Exception {

        URL aURL = new URL("http://example.com:80/docs/books/tutorial"
                           + "/index.html?name=networking#DOWNLOADING");

        System.out.println("protocol = " + aURL.getProtocol());
        System.out.println("authority = " + aURL.getAuthority());
        System.out.println("host = " + aURL.getHost());
        System.out.println("port = " + aURL.getPort());
        System.out.println("path = " + aURL.getPath());
        System.out.println("query = " + aURL.getQuery());
        System.out.println("filename = " + aURL.getFile());
        System.out.println("ref = " + aURL.getRef());
    }
}
```

控制台输出

>**protocol** = http  
**authority** = example.com:80  
**host** = example.com  
**port** = 80  
**path** = /docs/books/tutorial/index.html 
**query** = name=networking  
**filename** =/docs/books/tutorial/index.html?  name=networking  
**ref** = DOWNLOADING  

***
# 连接URL
***
## 直接读取URL

使用URL对象，通过调用URL.openStream()方法，我们可以直接获取其在网络上的资源。openStream()方法返回一个java.io.InputStream()对象，这样我们就可以像使用一般I/O流一样，进行读操作。下面是示例代码，其中我们使用了一个输入缓冲字符流BufferedReader来提高读取性能。

```java
 URL url = new URL("http://www.ifeng.com");

        try (BufferedReader reader = new BufferedReader(new InputStreamReader(url.openStream()))) {
            String line;
            while((line = reader.readLine()) != null){
                System.out.println(line);
            }
        }
```
控制台中将输出Html文档内容，这里I/O流使用了try-with-resource方式处理异常，所以我们不必自己关闭I/O流。

## 使用URLConnection连接

前面我们使用openStream()方法可以直接获取网络资源的输入流，但我们看下这个方法的源代码

```java 
public final InputStream openStream() throws java.io.IOException {
        return openConnection().getInputStream();
    }
```
其实这里是先调用了openConnection()方法，而openConnection()方法返回的是之前说过的URLConnection对象，它是代表应用程序和网络资源之间连接的抽象类。openConnection()方法会根据URL的协议名称返回相应的URLConnection子类，比如当使用http协议的时候，返回的就是HttpURLConnection对象,此时并不会有联网操作，只有当调用URLConnection.connect()方法时，才会实际建立与远程网络资源之间的通信连接。   
实际不必每次都使用connect方法初始化连接，一些依赖连接的方法比如getInputStream()和getOutputStream()会自动调用该方法进行连接。  
getInputStream()和getOutputStream()方法分别是从URLConnection连接中读取和写入流，获取流就是直接读取网络资源。写入流则是用于向服务器发送一些数据。很多网页上有一些表单，需要我们填写一些数据后发送到服务器端，然后服务端给我们一个响应。如果采用http的get请求，发送的查询数据直接在挂在url连接后，但是有些表单采用的post请求发送数据，这时就要使用一个OutputStream来写入需要的数据。
下面是一段示例代码，这里虚拟一个网址，需要我们向其发送一个查询关键字，然后服务器返回给我们一个查询后的静态html文档,我们再将其打印到控制台中。

```java

        URL url = new URL("http://example.com/search");

        URLConnection con = url.openConnection();
        con.setDoOutput(true);//设置连接允许写入数据

        try (OutputStream out = con.getOutputStream()) {
            out.write("keyword=cat".getBytes());
        }

        InputStreamReader in = new InputStreamReader(con.getInputStream());
        try (BufferedReader reader = new BufferedReader(in)) {
            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
        }

```