title: Linux基础学习（一）介绍
id: 1493977130879
author: 不识
tags:
  - Linux
categories:
  - Linux基础
date: 2017-04-25 17:39:00
---

# Linux分两个版本
- linux 内核版本 ：linux 内核版本www.kernel.org,

>内核版本说明：2.6.18  最新是4.104新版本作为测试等，不稳定
 
- Linux发行版本:在内核版本技术上进行裁剪，ubuntu，图形界面好，redhat流行
	- ubuntu，其实在服务器上很少装图形界面。
	- redhat，可以免费使用,但是有些功能收费,这个版本较为常用
	- centos,与redhat几乎一样,
	- fedora:个人版本，不同与windows个人版。fedora,是完整功能版。全功能版，不适合个人操纵。
	- suse, debian 等开发版本
    
>服务器用的比较多：centos

# Linux常见开源软件
- apache
- nginx 占用服务器资源更少,提高并发吞吐量
- mysql
- python
- monogoDB
- Ruby

# Linux应用场景
- Linux企业服务器
- 嵌入式应用

# Linux与Windows的区别
- Linux严格区分大小写,Windows的dos界面是不去分别大小写的
- Linux一切以文件的形式存在,包括硬件,用户等等,而Windows中硬件以设备管理器方式管理
- Linux不靠文件拓展名区分文件类型,而是以权限的方式管理,但实际使用时,会用不同后缀方便用户区分

# Linux分区与挂载
　　Linux没有盘符概念,而是称为挂载点.Linux用目录作为分区,而不是像windows一样用C,D等字母
- 必须分区
	- / 根分区
	- swap分区,相当于windows的虚拟内存
- 推荐分区
	- /boot 启动分区(200M)