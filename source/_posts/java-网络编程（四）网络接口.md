title: java 网络编程（四）网络接口
id: 1495427625848
author: 不识
tags:
  - java
  - 网络编程
categories:
  - java 基础
  - java 网络编程
date: 2017-05-22 12:34:00
---

系统通常运行多个活动网络连接，比如有线网，无线网和蓝牙。某些应用程序可能需要访问此信息才能在特定连接上执行特定的网络活动。 java.net.NetworkInterface课程提供对这些信息的访问。   
一个网络接口是一台计算机与私有或公有网络之间的互联点，通常是一个网卡，但是不一定是物理上的网卡。比如本地回环 IP“127.0.0.1”就不是一个物理设备而是一个软件模拟的网络接口，它通常用于测试本地网络环境。NetworkInterface可以代表这两种类型的接口。
NetworkInterface没有提供公共构造器，因此我们不能创建它的对象，但是它提供了一系列的静态方法，供我们来检索系统中的网络接口。
- **getByInetAddress()**
- **getByName()**
- **getNetwordInterfaces()**

前两种方法是需要我们已经知道了接口的IP地址或接口名，后一种则以Enumeration返回系统内所有的网络接口。

网络是可以分层组织的，NetworkInterface类还提供了两个方法
- **getParent()**
- **getSubInterfaces()**

如果一个网络接口是一个子接口的话调用getParent()返回其父接口，而getSubInterface()则返回一个接口的所有子接口。

我们还可以获取网络接口的IP地址或子网掩码等其他信息，这里有两个方法
- **getInetAddresses()**
- **getInterfaceAddress()**
getInetAddress()会返回一个Enumeration类型的InetAddress.而getInterfaceAddresses()则返回一个List类型包含InterfaceAddress元素。通过InterfaceAddress我们可以获取网络接口的IPv4地址子网掩码和广播地址，以及IPv6地址的网络前缀长度。


在前面Socket通信中，我们要给一个Socket绑定要发送到的IP地址，这里我们可以使用NetworkInterface来获得本地网卡的IP地址。

```java
NetworkInterface networkInterface = NetworkInterface.getByName("eth0");
InetAddress inetAddress = networkInterface.getInetAddresses().nextElement();

Socket socket = new Socket(inetAddress.getHostName(),8888);
```

同样在使用MulticastSocket时，也可以指定一个MultcastSocekt加入的群组接收数据包时使用的网络接口
```java
NetworkInterface nif = NetworkInterface.getByName("bge0");
MulticastSocket ms = new MulticastSocket();
ms.joinGroup(new InetSocketAddress(hostname, port), nif);
```