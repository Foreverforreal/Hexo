title: Linux基础学习（五）网络配置
id: 1494989421367
author: 不识
tags:
  - Linux
categories:
  - Linux基础
date: 2017-05-17 10:50:00
---
# Linux配置IP地址

## ifconfig命令临时配置
- **ifconfig**   
查看配置网络状态   
- **ifconfig** eth0 192.168.0.1 netmask 255.255.255.0  
临时设置etho0网卡的ip地址与子网掩码

## setup工具永久配置
setup是redhat中专有图形界面工具，按照选项，直接配置

<!-- more -->

## 修改网络配置文件
1. 网卡信息文件
/etc/sysconfig/network-scripts/ifcfg-eth0

|配置|意义|
|--------|----|
|DEVICE=eth0|网卡设备名称|
|BOOTPROTO=none|是否自动获取ip(none,static,dhcp)|
|HWADDR=00:0c:29:17:c4:09|MAC地址|
|NM\_CONTROLLED=yes|是否可以由Network Manager图形工具托管|
|ONBOOT=yes|是否随网络服务启动，eth0生效|
|TYPE=Ethernet|类型为以太网|
|UUID="XXX"|唯一标识码|
|IPADDR=192.168.0.252|IP地址|
|NETMASK=255.255.255.0|子网掩码|
|GATEWAY=192.168.0.1|网关|
|DNS1=202.106.0.20|DNS|
|IPV6INIT=no|IPV6没有启用|
|USERCTL=no|不允许非root用户控制此网卡|

2. 主机名文件  
/etc/sysconfig/network

>linux主机名不需要唯一，不影响局域网内互相联网，修改该配置文件中主机名，不会立即生效，需要重启计算机，但可以使用hostname 主机名，临时修改，立即生效

3. DNS配置文件  
/etc/resolv.conf

|配置|意义|
|----|----|
|nameserver 202.106.0.20| 名称服务器，DNS的ip|
|search localhost||


## 图形界面配置IP
easy

# 虚拟机网络参数配置