title: Linux基础学习（二）基本命令
id: 1493197195654
author: 不识
tags:
  - Linux
categories:
  - Linux基础
date: 2017-04-26 16:59:00
---
***
# 命令基本格式
***
命令行头部显示字符意义
> [root@localhost ~] #

|字符|意义|
|--|--|
|**root**|      用户名|
|**localhost**|   主机名|
|**~**| 当前所在目录，~表示当前用户home目录|
|**\#**| 用户权限，**\#**表示超级管理员用户，**$**表示普通用户|

> Linux管理员账号是root 

<!-- more -->
***
# 目录相关命令
***
## ls
ls 　\[选项] 　[目录名]　**查看目录下的文件**

|选项|意义|
|----|----|
|**-a**|显示所有文件,包括隐藏文件|
|**-l**| 显示详细信息,可简写为ll|
|**-d**| 查看目录属性|
|**-h**|人性化显示文件大小|
|**-i**|显示inode　　|

使用ls -l(ll)显示文件详细信息时，会显示
> <font color="blue">- rw- r-- r--.</font> 1 root root 24772 1月  14 18:17 install.log

蓝色部分表示文件权限，共有十位，第一位表示文件的类型，Linux系统没有文件扩展名

|字符|文件类型|
|----|--------|
|**-**|文件|
|**d**|目录|
|**l**|软链接|

后九位三个为一组，分别表示**所有者**，**所属组**，和**其他人**，字母表示该权限下用户的权限

|字母|权限|
|----|----|
|**r**|读取|
|**w**|写入|
|**x**|执行|

## mkdir
mkdir　\[选项]　[目录名]　创建目录，是makedirectories的缩写

|选项|意义|
|----|----|
|**p**|递归创建,也就是多级创建|

## cd

cd　[目录名]　切换目录 change directory

|选项|意义|
|----|----|
|**cd ~**|进入当前用户的home目录|
|**cd**| 显示详细信息,可简写为ll|
|**cd -**|进入上次目录|
|**cd ..**|进入上一级目录|
|**cd .**|进入当前目录，基本没用|

## rmdir
rmdir　[目录名]　删除目录　remove empty directory　只能用来删除空目录
## rm
rm　[选项]　[文件名]　删除文件  remove

|选项|意义|
|----|----|
|**-r**|删除目录|
|**-f**|强制删除|

## cp

cp　\[选项]　\[原文件或目录]　[目标目录]　复制文件或目录　copy

|选项|意义|
|----|----|
|**-r **|复制目录，不带的话只能复制文件|
|**-p**|连带文件属性复制|
|**-d**| 若源文件是链链接文件,则复制链接属性|
|**-a**|相当于-pdr|

## mv  

mv　\[源文件或目录]　[目标目录]　剪切目录或者重命名　move

***
# 链接处理命令
***

## ln

ln　\[选项]　[目标文件]　生成链接文件 link

|选项|意义|
|----|----|
|**-s **|创建软链接|

硬链接,一般不建议使用

1. 相同的inode和存储block,可以看作同一个文件,类比百度云上的文件  
2. 可以通过inode识别  
3. 不能跨区  
4. 不能针对目录操作
软连接,类似windows中的快捷方式,
1. 虽然有自己的inode和存储block,但block中只保存链接文件的文件名和inode 
2. 修改任意文件,另一个都改变
3. 删除原文件,软连接不能使用

软链接和硬链接有很多相似的地方,比如,两个文件之间只要修改然后一个另一个都会改变,但硬链接两个文件间没有依赖关系,删除其中一个另一个还存在,但是软链接删除原文件,链接文件就会显示错误.
>软链接如果要链接到的目标目录不再同一个目录下,原文件目录一定要用绝对路径.

***
# 搜索命令
***

## locate
locate　[文件名]　在后台数据库按文件名搜索,搜索速度更快
 > locate并不是在系统中搜索文件而是在  /var/lib/mlocate 这个后台数据库中搜索,新创建的文件需要先使用updatedb更新数据库才能搜索的到。此外有些目录下文件无法被locate搜索，因为这些目录被mlocate.db数据库所忽略，被忽略的目录在/etc/updatedb.conf里面进行配置。
 
## whereis

whereis　\[选项]　[命令名]　搜索命令二进制文件所在的路径以及帮助文档、配置文件所在位置

|选项|意义|
|----|----|
|**-b **|只看二进制文件|
|**-m **|只看帮助文件|

## which   

which　[命令名]　搜索命令所在的路径以及别名


## find

find　\[搜索范围]　[搜索条件]

> find 应当避免大范围搜索，会非常耗费系统资源  
> find 搜索文件名时是完全匹配，如果要模糊搜索需要使用通配符

**搜索条件**

|搜索命令|意义|
|----|----|
|**-iname**|忽略大小写|
|**-user**|按所有者搜索|
|**-nouser**|查找没有所有者的文件|
|**-mtime**|按文件内容修改时间搜索|
|**-atime**|按文件访问时间搜索|
|**-ctime**|按文件属性修改时间搜索|
|**-size **|按文件大小搜索|
|**-inum**|按inode编号搜索|


按时间搜索时，后面带时间范围，默认单位是天，  
- +10  表示>十天前，+理解为>号   
- 10     表示十天前当天   
- -10    表示十天内，-理解为<号  

 
按文件大小搜索时，后面带文件大小，默认单位是一个扇区块大小，512字节，千字节是k,兆是M，注意大小写　

> 如要要对搜索到的文件执行处理命令，在搜索命令后加上 -exec 命令 {} 、；

**常用通配符**

|通配符|意义|
|----|----|
|** * **|匹配任意字符|
|** ？ **|匹配任意一个字符|
|** [X] **| 匹配任意一个括号内字符|


**逻辑条件**

|逻辑命令|意义|
|----|----|
|**-a **|and与命令|
|**-o **|or 或命令|

## grep

grep　[选项]　\[字符串]　[文件名]　文件内容搜索，在文件中匹配符合条件的字符串

|选项|意义|
|----|----|
|**-i **|忽略大小写|
|**-v **|排除指定字符串|

>grep使用正则表达式匹配

***
# 帮助命令
***
## man
 man　[命令等级]　|　[选项]　［命令］　获取制定命令的帮助文档　manual
 
 |选项|意义|
|----|----|
|**-f**|查看命令作用和它的命令等级,**等同whatiis**|
|**-k**|查看所有包含给定命令的帮助文档,**等同aproros**|

>查看帮助文档时,可以输入“/”后输入要在命令文档中查找的字符，“n”键是查找下一个，“shift”查找上一个

## --help

命令  --help　直接查看该命令的帮助

## help
help [命令]  获取shell自带命令的帮助

## info

info [命令] 更详细的帮助文档


***
# 压缩与解压缩命令
***
linux系统中常见的压缩格式有 .zip .gz .bz2 .tar.gz  .tar.bz2

## .zip格式
### zip

zip　\[压缩文件名]　[源文件名]　压缩指定的文件

 |选项|意义|
|----|----|
|** -r **|压缩目录|
### unzip
unzip　[压缩文件名]　解压缩指定的压缩文件

## .gizp格式
### gzip
gzip　[源文件]　压缩并删除文件

|选项|意义|
|----|----|
|** -r **|压缩指定目录下所有子文件，但是不会压缩目录本身|
|** -d **|解压缩指定文件|

### gunzip

gunzip　[压缩文件]　解压指定文件

|选项|意义|
|----|----|
|** -r **|解压缩指定目录下所有文件|

## .bz2格式
### bzip2 
bzip2　[源文件]　压缩指定文件，不保留源文件，不能压缩目录

|选项|意义|
|----|----|
|**-k**|压缩时保留源文件|

### bunzip2 

bunzip2　[压缩文件名]　解压缩指定文件，不保留源文件

|选项|意义|
|----|----|
|**-k**| 保留压缩文件|

这三种压缩格式，压缩时对文件的处理方式不同　　　　

|压缩格式|压缩目录|保留源文件|
|----|----|------|
|**.zip**| √|√|
|**.gz**| O|√|
|**.bz2**| X|X|
　这些压缩格式中.zip格式可以压缩目录，而.gz只能压缩目录下所有文件，.bz2则根本不能压缩目录，对于这个问题，可以先使用打包命令，将目录打包后再进行压缩
## tar
tar是进行打包和解包的命令，当执行打包操作时，需要指定需要打包目录，和打包后的包名，解包时只需要给定包名

|选项|意义|
|----|----|
|** -cvf **|打包指定文件|
|** -xvf **|解包指定文件|
|** -tvf  **|查看打包文件夹内容|
|** -cvf **|打包指定文件|
以上是直接打包解包操作，为了方便操作，还可以和压缩解压命令结合使用，只需要在打包解包前加上一个字母，即可压缩解压特定格式    

|字母|格式|
|----|----|
|**z**|.gz|
|**j**|.bz2|

>如果要指定解包位置的话，在命令后加-C [目录名]

***
# 关机重启命令
***
## shutdown
shutdown [选项] 时间

|选项|意义|
|----|----|
|**-c**| 取消前一个关机命令|
|**-h**| 关机|
|**-r**| 重启|

>其他的关机命令，halt ,poweroff, init 0但这些命令不能保证安全关机      
>其他重启命令 ，reboot ,init 6，reboot是比较安全的重启命令

***
# 查看登陆用户信息命令
***

## w
查看系统中所有登陆用户详细信息
## who
查看系统中所有登陆用户简略信息
- 命令输出
	- 用户名
   - 登陆终端
   - 登陆时间（以及登陆ip）
   
## last
查看系统中所有用户的登陆信息
- last命令默认读取/var/log/wtmp文件
- 命令输出 
	- 用户名
   - 登陆终端
   - 登陆ip
   - 登陆时间
   - 退出时间（在线时间）
   
## lastlog
查看所有用户的最后一次登录时间
- lastlog命令默认读取的是/var/log/lastlog文件内容
- 命令输出
	- 用户名
   - 登陆终端
   - 登陆ip
   - 最后一次登录时间

***
# 其他命令
***
## mout
mout 查看系统中已经挂载的设备

|选项|意义|
|----|----|
|**-a**|根据/etc/fstab配置文件，自动挂载设备|

mout \[-t 文件系统] [-o 特殊选项] 设备文件名 挂载点
>-t 文件系统：加入文件系统来指定挂在的类型，可以ext3,ext4,iso9660等文件系统  
>-o 特殊选项：可以指定挂在的额外选项

## umount
umount [设备文件名或挂载点]   卸载挂载设备

- 挂载光盘    
 mount -t iso9660 /dev/cdrom/ /mnt/cdrom
 umount /mnt/cdrom
 
- 挂载u盘
 mount -t vfat /dev/sdb1 /mnt/usb
 umout /mnt/usb
***
# 常用目录作用
***
|目录|作用|
|----|----|
|**/　**　|根目录
|**/bin** |命令保存目录
|**/sbin**|命令保存目录(超级用户才能使用目录)
|**/proc 、/sys**|内存挂载点,不能操作,直接写入内存的
|**/boot**|启动目录,启动相关文件
|**/dev**|设备文件保存目录
|**/etc**|配置文件保存目录
|**/usr**|系统软件资源目录
|**/var**|系统文档目录
|**/home**|普通用户家目录
|**/root**|管理员用户目录
|**/lib**|系统库目录 
|**/mnt**|系统挂载目录，U盘之类
|**/media**|挂载目录,光盘之类
|**/misc**|挂载目录，磁带之类
|**/tmp **|临时目录