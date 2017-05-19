title: 'java 网络编程（二）HTTP '
id: 1494923723616
author: 不识
tags:
  - java
  - 网络编程
categories: []
date: 2017-05-16 16:44:00
---
HttpURLConnection是一个支持HTTP特定功能的URLConnection。每个HttpURLConnection实例用于创建单个请求，但是到HTTP服务器的底层网络连接可以被其他实例共享。在请求之后，需要调用HttpURLConnection上InputStream和OutputStream的close()方法，来释放相关的网络资源，但是该方法对共享的持久化连接没有影响。可以调用disconnect()方法来关闭底层套接字，如果一个持久化连接闲置的话。

<!-- more -->

# URLConnection缓存API

HTTP通常用于分布式信息系统，通过使用响应缓存可以提高性能。虽然HTTP代理服务器通常自己缓冲最近访问的网络资源，但是有时它也希望有一个本地缓存，比如浏览器缓冲。

![ResponseCache](/images/java base/ResponseCache.gif)

在java.net包中有三个缓存相关的抽象类
- **ResponseCache**
- **CacheRequest**
- **CacheResponse**

## ResponseCache
一个ResponseCache的具体子类代表一个URLConnection缓存。这种子类实例可以通过调用ResponseCache.setDefault()方法来注册进系统内。并且系统会按照一下顺序来调用这些对象。
- 存储那些已经被从外部资源检索的资源数据到缓存中
- 尝试调取可能存储在缓存中的可被请求资源

ResponseCache有两个方法：get()返回一个基于URI和请求头的CacheResponse。put()允许缓存决定是否资源应该被存储，并且返回CacheRequest。

## CacheRequest
一个CacheRequest的具体子类用于在ResponseCache中写入一个条目。这种类的实例提供了一个由协议处理程序调用，用来存储资源数据到缓冲中的OutputStream对象。同时还有一个abort()方法，可以允许缓存打断或放弃一个存储操作。

CacheRequest类有两个方法:getBody()返回一个用于将请求体写入缓存的流；abort()方法放弃缓存写入。

## CacheResponse
一个CacheResponse的具体子类从ResponseCache中返回一个条目。这种类的实例提供一个返回实体主体的InputStream，还有一个getHeaders()方法，返回相应的响应头。

CacheResponse类有两种方法：getBody()返回从缓存读取请求体的流；而getHeaders()返回存储的响应头。

# Cookie管理

HTTP状态管理机制指定了一种使用HTTP请求和响应创建有状态会话的方法。

## JDK 5.0之前
在JDK 5.0之前可以向应用程序添加Cookie管理，但是提供的API有些基础。没有一点和cookie管理有关联。每个应用程序必须通过使用java.net.URLConnection类中的以下两种方法单独处理每个HTTP请求。响应的cookie.
- setRequestProperty()
- getHeaderFileds()

在发送HTTP请求前应该调用第一种方法，以便为HTTP头中的的当前URL设置适当的cookie。而第二种方法应当在从由HTTP服务器发送的响应头中检索cookies时调用。

这种方法容易分散代码，且容易出错不易维护。

## JDK 5.0后
在JDK 5.0中通过一个抽象类引入了一个新的回调机制，将HTTP状态管理策略的实现连接到HTTP协议处理程序中。应用程序和Web容器可以通过提供新API的具体子类来引入Cookie管理。

新的抽象类是java.net.CookieHandler。它提供了一种注册和检索JVM中当前CookieHandler的机制，以及检索和记录特定URI的相关Cookie的方法。

在CookieHandler中有两个静态方法：getDefault()和setDefault()用来在虚拟机中检索和注册默认的CookieHandler。还有两个实例方法：get()和put()分别用来返回基于URL的cookie列表，存储来自响应头中cookies的列表。

cookies是使用一个Map&lt;String,List&lt;String&gt;&gt;来表示，一个由cookie头字段名称映射到由字符串表示的cookie的List的键值对。目前为止定义了两个状态管理头“Set-Cookie2”和“Cookie”.前一个用来返回相应头中的cookeis，后一个是在HTTP请求头中设置cookies。

目前在JavaSE中没有cookie管理的实现类，但在Java Plugin 和 Java WebStart 中提供了默认的CookieHandler。

# HTTP验证

Authenticator是一个抽象类，但是它没有抽象方法，可以由应用程序来实现继承使用。调用该类可以获取用户名和密码来用于认证交互。

## 继承Authenticator 

继承Authenticator的应用程序代码必须重写getPasswordAuthenticator()，需要注意的只这个方法并不是抽象方法，但是Authenticator中只是简单返回了一个null。下面是一个小的实现

```java
    class MyAuthenticator extends Authenticator {

        public PasswordAuthentication getPasswordAuthentication () {
            return new PasswordAuthentication ("user", "password".toCharArray());
        }
    }
```

上面例子中，只是为HTTP的认证交互返回了一个用户的用户名和密码。实际应用过程会使用到Authenticator的其他来获取关于HTTP请求更多的需要验证的信息。下面这些方法都可以由getPasswordAuthenticator()方法的实现调用，用来决定如获取处理每个请求。

- getRequestingHost()
- getRequestingPort()
- getRequestingPrompt()
- getRequestingProtocol()
- getRequestingScheme()
- getRequestingURL()
- getRequestingSite()
- getRequesttorType() 返回请求者是一台服务器还是代理

## 开启验证

定义好一个合适的验证实现类后，可以通过以下调用来开启验证
```java
 Authenticator.setDefault (authinstance);
```
其中authinstance是一个声明的实现类实例。如果不调用该方法的话，其禁用身份验证，并通过IOException异常将服务器身份验证错误返回给用户代码。一旦注册验证，http实现将尝试自动获取用户凭据来进行身份验证（通过缓存中的凭据或从系统获取的凭据）。如果正确的凭据不可用，则会调用该用户验证器来提供。
## 控制使用的验证方案
当服务器有一个客户端请求需要验证的时候，可能会给客户端提供一些验证方案（比如摘要验证和NTLM身份验证），然后客户端可以从中选择一个，通常应用程序不关系哪个方案被使用，并且自动实现选择一个安全性最高的协议。

如果用户需要确保使用特定方案，则可以设置以下系统属性来修改默认行为。

 >-Dhttp.auth.preference="scheme"
 
 -D 用来指定要在命令行上要设置的属性。 "http.auth.preference" 是属性名，"scheme"则是要使用的验证方案名。如果服务器的方案列表中不包含此方案，则会选择默认方案来验证。

## 验证方案
### 基本验证
基本验证是一种简单，但是非常不安全的验证方案。用户名和密码都是通过BASE64编码，因此它可以被任何一个能够访问该数据包的人获取这些信息。基本验证的安全性可以通过使用HTTPS协议来提高，从而加密请求和响应。getRequestingPrompt()方法返回由服务器提供的基本认证领域(Basic authentication realm)。

### 摘要验证

Digest是使用MD5哈希算法的基于用户名和密码的密码散列的相对安全的方案。Digest还提供服务器向客户端证明它也知道共享密钥（密码）的能力。这种行为通常被禁用，因为并不是所有的服务器都支持它。可以使用以下系统属性启用此功能：    
> -Dhttp.auth.digest.validateServer =“true”   
> -Dhttp.auth.digest.validateProxy =“true” 

getRequestingPrompt（）方法返回由服务器提供的摘要认证领域(Digest  authentication realm)。

### NTLM
NTLM是微软定义的一种验证方案，它的安全性介于基本验证和摘要验证之间。NTLM可以与代理或服务器一起使用，但是不能两者同时使用。如果正在使用代理，则不能用于服务器身份验证。这是因为协议实际是验证TCP里拦截，而不是单独的HTTP交互。

在Microsoft Windows平台上，NTLM身份验证尝试从系统中获取用户凭据，而不会提示用户的身份验证对象。如果这些凭据不被服务器接受，则用户的验证器将被调用。

由于Authenticator类在支持NTLM出现就定义了，因此无法在API中添加对NTLM域字段的支持，下面有三种对该域的选择

- 在某些环境中不要指定它，域实际上并不是必需的，应用程序不需要指定。
- 域名可以使用在用户名前加域名前缀，然后在用户名前添加一个反斜线“\”的方式进行编码。使用此方法，只要使用者知道必须使用此符号，则不需要修改使用Authenticator类的现有应用程序。
- 如果未在方法2中指定域名，并且定义了系统属性“http.auth.ntlm.domain”，则该属性的值将被用作域名。

### Http Negotiate（SPNEGO）

# 持久连接(Persistent Connections)

HTTP持久连接也被称为HTTP活动保持（keep-alive）或者HTTP连接重用（connection reuse）,它是使用相同TCP连接发送和接收多个HTTP请求/响应的思想，而不是为每个单个请求/响应对打开一个新的TCP连接。使用持久连接对于提高HTTP性能非常重要。

使用持久连接有以下几个优点

- 对网络友好。由于更少的创建和关闭TCP连接，这减少了网络通信。
- 由于避免了初始化TCP握手，减少了后续请求的延迟。
- 持续更久连接允许TCP足够的时间来确定网络的拥塞状态，从而进行适当的反应。

当在HTTPS或者使用SSL/TLS的HTTP中时，这些优点可能显得更加明显。这里，除了初始TCP连接建立之外，持续连接可能减少昂贵的SSL / TLS握手数以建立安全关联。

在HTTP / 1.1中，持久连接是任何连接的默认行为。也就是说，除非另有说明，否则客户端应该假定服务器将维护持久连接，即使在服务器发生错误响应之后。但是，该协议提供了一种客户端和服务器发出关闭TCP连接信号的手段。

## 当前的JDK如何处理Keep-Alive？
JDK同时支持HTTP/1.1 和 HTTP/1.0。
当应用程序读取完响应体内容后或者调用 close() 关闭了URLConnection.getInputStream()返回的流，JDK中的HTTP协议句柄将关闭连接，并将连接放到连接缓存中，以便后面的HTTP请求使用。
对HTTP keep-Alive 的支持是显然的。但是，你也可以通过系统属性http.keepAlive和http.maxConnections以及HTTP/1.1协议中的特定的请求响应头来控制。

控制Keep-Alive表现的系统属性有：

>http.keepAlive=&lt;boolean>
>默认: true
>指定长连接是否支持

>http.maxConnections=&lt;int>
>默认: 5
>指定对同一个服务器保持的长连接的最大个数。

影响长连接的HTTP header是：Connection: close如果请求或响应中的Connection header被指定为close，表示在当前请求或响应完成后将关闭TCP连接。

JDK中的当前实现不支持缓存响应体，所以应用程序必须读取完响应体内容或者调用close()关闭流并丢弃未读内容来重用连接。此外，当前实现在清理连接时并未使用阻塞读，这就意味这如果响应体不可用，连接将不能被重用。

## JDK1.5中的新特性
当应用接收到400或500的HTTP响应时，它将忽略IOException 而另发一个HTTP 请求。这种情况下，底层的TCP连接将不会再保持，因为响应内容还在等待被读取，socket 连接未清理，不能被重用。应用可以在捕获IOException 以后调用HttpURLConnection.getErrorStream() ，读取响应内容然后关闭流。但是现存的应用没有这么做，不能体现出长连接的优势。为了解决这个问题，我们引入了一个解决方法。

解决方法是缓冲响应体，如果响应码大于等于400的话，如果在一定的时间限制内达到一定的数量，则释放底层的socket连接来重用。基本原理是当响应状态码大于或等于400时，服务器端会发送一个简短的响应体来指明连接谁以及如何恢复连接。

下面介绍一些SUN实现中的特定属性来帮助接收到错误响应体后清理连接：
主要的一个是：
sun.net.http.errorstream.enableBuffering=&lt;boolean&gt;  
默认: false

当上面属性设置为true后，在接收到响应码大于或等于400是，HTTP 句柄将尝试缓存响应内容。释放底层的socket连接来重用。所以，即便应用不调用getErrorStream()来读取响应内容，或者调用 close()关闭流，底层的socket连接也将保持连接状态。

下面的两个系统属性是为了更进一步控制错误流的缓存行为：
sun.net.http.errorstream.timeout=&lt;int> in 毫秒  
默认: 300 毫秒   
sun.net.http.errorstream.bufferSize=&lt;int> in bytes  
默认: 4096 bytes

## 你如何做可以保持连接为连接状态呢？
不要忽略响应体而丢弃连接。这样会是TCP连接闲置，当不再被引用后将会被垃圾回收器回收。   
如果getInputStream()返回成功，读取全部响应内容。如果抛出IOException ，捕获异常并调用getErrorStream() 读取响应内容（如果存在响应内容）。  

即便你对响应内容不感兴趣，也要读取它，以便清理连接。但是，如果响应内容很长，你读取到开始部分后就不感兴趣了，可以调用close()来关闭流。值得注意的是，其他部分的数据已在读取中，所以连接将不能被清理进而被重用。  
下面是一些示例代码  
```java
try {
        URL a = new URL(args[0]);
        URLConnection urlc = a.openConnection();
        is = conn.getInputStream();
        int ret = 0;
        while ((ret = is.read(buf)) > 0) {
          processBuf(buf);
        }
        // 关闭输入流
        is.close();
} catch (IOException e) {
        try {
                respCode = ((HttpURLConnection)conn).getResponseCode();
                es = ((HttpURLConnection)conn).getErrorStream();
                int ret = 0;
                // 读取响应体
                while ((ret = es.read(buf)) > 0) {
                        processBuf(buf);
                }
                // 关闭错误流
                es.close();
        } catch(IOException ex) {
                // 处理异常
        }
}

```

如果你预先就对响应内容不感兴趣，你可以使用HEAD 请求来代替GET 请求。例如，获取web资源的meta信息或者测试它的有效性，可访问性以及最近的修改。下面是代码片段：

```java
URL a = new URL(args[0]);
URLConnection urlc = a.openConnection();
HttpURLConnection httpc = (HttpURLConnection)urlc;
// only interested in the length of the resource
httpc.setRequestMethod("HEAD");
int len = httpc.getContentLength();
```

## JDK 6中的更改
在JDK 6 之前，如果应用程序在少量数据仍然被读取时关闭HTTP InputStream，则连接必须被关闭，而不是被缓存。现在在JDK 6中，则是从后台线程中的连接中一直读取到高达512 KB，从而允许连接被重用。可以通过http.KeepAlive.remainingData系统属性配置可以读取的确切数据量。