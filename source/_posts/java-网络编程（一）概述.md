title: java 网络编程（一）概述
id: 1494905730133
author: 不识
tags:
  - java
  - 网络编程
categories:
  - java 基础
  - java 网络编程
date: 2017-05-16 11:35:00
---
# 地址
java.net提供了以下地址相关类

- **InetAddress**
- **Inet4Address**
- **Inet6Address**
- **SocketAddress**
- **InetSocketAddress**

对于IP地址，有InetAddress,Inet4Address和Inet6Address三个类可用。InetAddress表示IP地址，它是IP使用的32位或128位无符号数，用来构建TCP和UDP协议的下级协议。Inet4Address提供IPv4地址，Inet6Address提供IPv6地址，他们都是InetAddress的子类。    
<!-- more -->

对于套接字地址，提供了SocketAddress和InetSocketAddress两个类。SockeAddress是一个抽象的独立于特定协议的套接字地址，是一个抽象类。InetSocketAddress 是SocketAddress的子类，它表示一个IP套接字地址，它有三种表示形式

- 一个IP地址和一个端口号（192.168.0.01：8888）
- 一个主机名和一个端口号（localhost：8888）
- 只有端口号（8888）

后一种是假设有一个通配符的IP地址。

# 建立TCP连接
以下类用来建立一个正常的TCP连接
- ServerSocket
- Socket

对于客户端和服务端之间的简单连接，ServerSocket和Socket是经常会使用到的。

ServerSocket表示服务器上等待并监听客户端服务请求的套接字。一个socket表示一个服务器和客户端之间通信的端点。当服务器获得服务请求时，它创建一个socket与客户端进行通信，并且在ServerSocket上继续收听其他请求 。客户端同样创建一个 socket与服务器进行通信。序列如下所示：
![TCP Connect](/images/other/sockets.png)
一旦建立连接，getInputStream()和getOutputStream()可以用来套接字之间的通信。
# 通过UDP接收/发送数据包
以下类用来通过UDP接收和发送数据包
- DatagramPacket
- DatagramSocket

DatagramPacket表示一个数据包。数据报包用于无连接传送，通常包括目的地址和端口信息。 DatagramSocket是用于通过UDP经由网络发送和接收数据包的套接字。DatagramSocket通过调用send(DatagramPacket dp)方法来发送一个DatagramPacket，同样通过调用recevie(DatagramPacket dp)来接收一个DatagramPacket（MulticastSocket可能接收/发送一个DatagramPacket到一个群组，它是DatagramSocket的子类，额外添加了多点广播功能）


# 查找/识别网络资源

以下类用于查找和识别网络资源.
- URI
- URL 
- URLClassLoader
- URLConnection
- URLStreamHandler
- HttpURLConnection
- JarURLConnection

这里最常使用的类是URI,URL,URLConnection和HttpURLConnection.

URI代表一个统一资源标识符，它是资源的标识符，但不一定是该资源的定位符。URL代表资源的统一资源定位符。URL是URI的一个子集，尽管类URL不是 URI的子类。简而言之，URL描述了如何访问资源，而URI可能是也可能不是。统一资源名（URN）是URI的另一个子集，但是没有它的java类存在。

URLConnection是表示所有应用程序和以URL标识的网络资源之间连接的抽象父类。给定一个URL和一个协议，URL.openConnection()返回一个URLConnection的适当实现的实例（协议在URL是已知的）。这个实例提供了URLConnection.connect()实际打开连接，访问URL的方法。

HttpURLConnection是最常使用的URLConnection实现类。它使用http协议，该协议用来访问Web服务器上的内容。

# 安全性（Security）

安全性包括认证和权限相关的类。认证涉及到用户认证，包括用户名和密码的检查。用户的身份验证可能需要在一定的场景下，比如当一个用户尝试访问一个URL。权限涉及到可能执行的操作，例如，除非存在NetPermission对的的setDefaultAuthenticator存在，否则调用Autherticator.setDefault(Authenticator a)会导致一个安全异常；
## 验证（Authentication）
一些代理和源服务器需要使用验证方案比如基本验证（BASIC）和摘要验证（DIGEST）来验证信息。例如，当使用http通过一个代理来连接时，这个代理需要验证，我们调用Authenticator类来获取用户名，密码，以及其他需要验证的条目。下面这些类和验证有关
- Authenticator
- PasswordAuthentication

除了用户验证方法外，抽象类Authenticator还有用于查询正在请求验证的方法（getRequestingXXX()）。这些通常是子类，一个子类的实例通过调用serDefault(Authenticator a)来向系统注册。(注意如果这里有一个安全管理器，则会检查安全策略是否允许NetPermission"setDefaultAuthenticator")然后，当系统需要验证的时候，它会调用一个方法比如requestPasswordAuthentication().

PasswordAuthentication只是一个用户名和密码数据的容器。

## 权限（Permissions）

- SocketPermission
- NetPermisson

一个SocketPermisson由一个主机，可选的端口号范围，和一组在主机上可能执行的操作：connect，accept,listen和/或resolve组成。它包括确定是否一个SocketPermission和另一个相等或意味着另一个Permission的方法。为了更容易检查一个权限是否存在，Socketpermission可能会被包含在一个PermissinCollection中。

NetPermission是一个各种网络权限命名的类。一个这儿有三个：setDefaultAuthenticator，requestPasswordAuthenticator,以及specifyStreamHandler。为了更容易检查一个权限是否存在，NetPermission可能会被包含在一个PermissinCollection中。