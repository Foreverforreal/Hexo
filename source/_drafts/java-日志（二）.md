title: java 日志（二）
id: 1495623286554
author: 不识
date: 2017-05-24 19:11:26
tags:
---

下图是Java Logging API的工作图 
![Logging Overview](/images/java base/Logging Overview.png)
所有的日志记录都是通过Logger实例来完成。Logger收集要记录的日志数据到一个LogRcord对象中。然后LogRecord被转发到一个Handler。Handler决定如何来处理LogRecord。比如将LogRecord写入到硬盘，或者通过网络发送到监控系统。

Logger和Handler都可以通过一个Filter来传递LogRecord。而Filter是来决定LogRecord是否应该被转发。

一个Handler还可以在LogRecord被发送到外部硬盘或系统前，使用Formatter将其格式化为一个字符串。