title: Linux基础学习（三）Shell基础
id: 1493618113985
author: 不识
tags:
  - Linux
categories:
  - Linux基础
date: 2017-05-01 13:55:00
---
# Shell概述
***
Shell是一个命令行解释器，它为用户提供了一个向Linux内核发送请求以便运行程序的界面系统级程序，用户可以用Shell来启动，挂起，停止甚至编写一些程序。   
Shell还是一个功能相当强大的编程语言，易编写，易调试，灵活性较强。Shell是解释执行的脚本语言

<!-- more -->
## Shell分类
- **Bourne Shell**：从1979年起Unix就开始使用Bourne Shell，它的主文件名为sh
- **C Shell**:C Shell主要在BSD版的Unix系统中使用，其语法和C语言类似而得名
-
> Shell主要语法类型有上面两种，这两种语法彼此不兼容。Bourne家族主要包括sh,ksh,Bash，psh,zsh;C家族余姚包括csh,tcsh   
> Linux就是使用Bash作为用户的基本Shell.Bash可以和sh互相兼容

***
# 脚本执行方式
***

## echo
echo \[选项] [输出内容] 输出后面的字符 

|选项|意义|
|----|----|
|**-e**|支持转义字符|

## 脚本编写
创建脚本文件名以.sh结尾，表示是一个脚本。
``` bash
#!/bin/bash
#this is the first program
echo -e "hello world"

```

> #!/bin/bash  标称下面是linux标准脚本，不能省略

## 脚本运行
脚本运行有两种方式
- 赋予执行权限，直接运行
``` bash
 chmod 755 hello.sh
 ./hello.sh
```
- 通过bash调用执行脚本
```bash
 bash hello.sh
```

***
# Bash的基本功能
***

## 命令别名与快捷键
### alias 
- alias  
查看系统中所有的别名  
- alias [别名]=[原命令]   
将命令命名为别名
>这种定义别名只是临时有效，系统重启后就会失效   
- vi ~/.bashrc  
将别名写入环境变量配置文件
> 这种定义别名永久生效，但需要重新登陆，如果不想重新登陆，执行source ~/.bashrc
- unalias [别名]
删除别名



