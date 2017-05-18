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
在JDK 5.0之前可以向应用程序添加Cookie管理，但是提供的API有些基础。没有一点和cookie管理挂钩。每个应用程序必须通过使用java.net.URLConnection类中的以下两种方法单独处理每个HTTP请求。响应的cookie.
- setRequestProperty()
- getHeaderFileds()
在发送HTTP请求前应该调用第一种方法，以便为HTTP头中的的当前URL设置适当的cookie。而第二种方法应当在从由HTTP服务器发送的响应头中检索cookies时调用。

这种方法容易分散代码，且容易出错不易维护。

## JDK 5.0后
在JDK 5.0中通过一个抽象类引入了一个新的回调机制，将HTTP状态管理策略的实现连接到HTTP协议处理程序中。应用程序和Web容器可以通过提供新API的具体子类来引入Cookie管理。

新的抽象类是java.net.CookieHandler。它提供了一种主次和检索JVM中当前CookieHandler的机制，以及检索和记录特定URI的相关Cookie的方法。

在CookieHandler中有两个静态方法：getDefault()和setDefault()用来在虚拟机中检索和注册默认的CookieHandler。还有两个实例方法：get()和put()分别用来返回基于URL的cookie列表，存储来自响应头中cookies的列表。

cookies是使用一个Map&lt;String,List&lt;String&gt;&gt;来表示，一个由cookie头字段名称映射到由字符串表示的cookie的List的键值对。目前为止定义了两个状态管理头“Set-Cookie2”和“Cookie”.前一个用来返回相应头中的cookeis，后一个是在HTTP请求头中设置cookies。

目前在JavaSE中没有cookie管理的实现类，但在Java Plugin 和 Java WebStart 中提供了默认的CookieHandler。

## HTTP验证