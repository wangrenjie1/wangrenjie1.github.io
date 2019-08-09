---
title: 前端必备Linux相关知识
date: 2019-06-19 16:07:17
tags: ['linux']
categories: Linux
---
# 前端必备Linux相关知识

## 操作系统的概述

- 适合工作和娱乐的windows
- 适合开发的Linux 、Unix 内核为 kernel
- 非常好用的 macOS
- linux 相关的发行版本  | CentOS  redhat |   Fedora   Debian  ubuntu |

## 远程链接服务器

利用 Cmder

`ssh root@192.168.x.x`
第一次登录 记得保存指纹文件

~ 代表 home目录
@ 之前是当前登录用户名
@ 之后是服务器名字

## 重要的 Linux命令

- 行编辑器 vi/vim       
    i   切换 插入模式    
    esc 退回浏览模式    
    :   变成命令状态    
    q   退出  
    w  写入保存  
    :wq!  强制保存退出       
    /xx  查找      n 下一个   
- 输入法  i - bus        
  查看 cat 
- 服务管理命令   systemctl         mac下  launchctl         
    查看nginx状态   systemctl status nginx 
    开启nginx服务   systemctl start nginx  
    关闭服务  kill  pid    要杀掉主进程(master)   不要杀掉工作进程(worker 受到保护)  | pkill -9 pid  必须退出
    筛选  ps aux | grep nginx
- 网络管理命令  ifconfig  、 ip命令、 router
  linux 命令下    ip addr   查看ip地址    ifconfig            ip后面那个 / 后面 是子网掩码 24 代表 255.255.255.0
  mac命令下没有ip命令
- pc aux  查看进程
- ss -anp | grep 80  查看80端口
    -a 所有的信息
    -n 网络
    -p 查看进程信息

- 下载命令 curl  wget
-   man 手册

## 常用Linux终端快捷键

- ctrl + c  结束正在运行的程序 【ping、telnet等】
- ctrl + d 结束输入或退出shell
- ctrl + s 暂停屏幕输出
- ctrl + q 恢复屏幕输出
- ctrl + l  清屏，等同于clear
- ctrl + a /  ctrl + e  快速移动光标到行首/行尾
  
## 进程、线程、携程

- 进程的目的就是担当分配系统资源（CPU时间、内存）的实体
- 线程是操作系统能够进行运算调度的最小单位
- 协程是一种用户态的轻量级线程，无法利用多核资源。

操作系统内核提供的api 调度 进程和线程  

一个进程里面至少有一个线程


## Linux免密远程登录

非对称加密

1.  生成密钥对 
    `ssh-keygen -t rsa -C "你自己的名字" -f "你自己的名字_rsa"`
    -t  指定加密算法  一般用  rsa
    -C  需要嵌入密钥的自定义名字  注意 需要加双引号
    -f   生成的密钥的密钥文件名字

    回车 提示你 要不要给密钥文件加密码 别给它加密码 否则你还是没法实现免密登陆
    
    生成两个文件   没有后缀的是 私钥     有后缀的是公钥   xxx.pub
2. 上传配置公钥
    上传公钥到服务器对应账号的home路径下的 .ssh/ 中 （`ssh-copy-id -i "公钥文件名" 用户名@服务器ip或域名`）
    检查 公钥文件权限 是否为 600

    -i 指定密钥

3. 配置本地私钥
    把第一步生成的私钥复制到你的home目录的 .ssh/路径下
    检查你的私钥文件权限是否为 600  如果不是 chmod修改权限

4. 免密登陆功能的本地配置文件
   修改 .ssh/config 配置文件  如果没有 config文件   `touch config`新建一个
    然后检查config文件是否为 644

单主机配置
```
    Host 主机别名
    User 登陆身份
    HostName 服务器IP或绑定的域名          不能带协议
    IdentityFile ~/.ssh/私钥文件名            私钥的路径    ~代表当前目录的home 目录  剩下 照抄
    Protocol 2                                            协议版本                                  
    Compression yes                                         
    ServerAliveInterval 60                        
    ServerAliveCountMax 20
    LogLevel INFO
```
多主机配置

```
    Host a-produce
    HostName IP或绑定的域名
    Port 22
    Host b-produce
    HostName IP或绑定的域名
    Port 22
    Host c-produce
    HostName IP或绑定的域名
    Port 22

    Host *-produce
    User 登陆身份
    IdentityFile ~/.ssh/私钥文件
    Protocol 2         
    Compression yes
    ServerAliveInterval 60
    ServerAliveCountMax 20
    LogLevel INFO
```
##  常见目录
- /boot    系统引导文件
- /etc      存放系统管理理和配置⽂文件
- /home    存放所有用户文件的根目录，是⽤户主目录的基点，⽐如用户user的主目录就
是/home/user，可以⽤~user表示
- /proc     虚拟⽂件系统⽬录，是系统内存的映射。可直接访问这个目录来获取系统信息。
- /root     root用户的专用home
- /bin       存放⼆二进制可执⾏行行⽂文件(ls,cat,mkdir等)，常⽤用命令⼀一般都在这⾥。
- /usr      用于存放系统级的应用程序
- /opt      额外安装的可选应用程序存放位置
- /dev      用于存放设备文件

## 命令基本格式

- `[root@xiaoming ~]`    
  - root 当前登录⽤户
  - xiaoming 主机名
  - ~ 当前工作目录，默认是当前用户的home目录，root就是 /root，普通用户是 /home/用户名
  - 提示符  超级用户是# ，普通用户是$

- 命令格式  命令 [选项] [参数]
  - 例如 ls -al  *.ini

- d rwx r-x r-x 
  - d 代表文件类型
  - 第一组 对应文件所有者权限
  - 第二组 对应所有者所在的用户组权限
  - 第三组  对应其他用户权限
  - 421  掩码标识
  - chmod +r +x +w  用户名    /   chmod  755  目录名 -R  

## 文件搜索命令
-  locate
  - 用文件名查找文件
- whereis
 - 搜索命令所在路径 
- find 
  - 文件搜索命令
  - find [搜索范围]  [搜索条件]

- 软连接 硬链接
   - 软连接 相当于 windows的快捷方式
   - 硬链接 文件节点  指向的文件删掉  硬链接就消失了
 
 - top  查看内存 

## 压缩与解压缩命令
- zip格式
  - 压缩文件    zip 压缩文件名 源文件
  - 压缩目录    zip -r 压缩文件名 源文件
  - 解压  unzip  压缩文件名
- gzip格式
  - gzip 源文件            //压缩为。gz格式的压缩文件，源文件会消失
  - gzip -c 源文件 > 压缩文件   //压缩为.gz格式的压缩文件，源文件不会消失
  - gzip -r 目录           //压缩目录下的所有子文件 ，但是不压缩目录
  - gzip -d 压缩文件名    //解压缩文件，不保留压缩包
  - gunzip 压缩文件         // 解压缩文件，不保留压缩包 

- bz2格式
   - bzip2 源文件    //压缩为 .bz2格式的文件，不保留源文件
   - bzip2 -k 源文件   //压缩为 .bz2格式的文件，保留源文件
   - bzip2 -d 压缩文件名  //解压压缩包
   - bupzip2  压缩文件名  //解压压缩包
- tar 命令
  - 打包命令
  - tar -cvf 打包文件名 源文件
    -     -c 打包
    -     -v 显示过程
    -     -f 指定打包后的文件名  
    -     -x  解打包
    -     -z：有gzip属性的
    -     -j：有bz2属性的

> 总结：
1、*.tar 用 tar -xvf 解压
2、*.gz 用 gzip -d或者gunzip 解压
3、*.tar.gz和*.tgz 用 tar -xzf 解压
4、*.bz2 用 bzip2 -d或者用bunzip2 解压
5、*.tar.bz2用tar -xjf 解压
6、*.Z 用 uncompress 解压
7、*.tar.Z 用tar -xZf 解压
8、*.rar 用 unrar e解压
9、*.zip 用 unzip 解压

## 关机和重启命令

- shutdown
  -     -c 取消前一个关机命令
  -     -h 关机
  -     -r 重启

shutdown now -p   // now  立刻马上   -p 断电指令

- logout    //退出终端
- exit

## 用户管理命令

- w    //查看登录用户信息
- who   //跟w差不多
- last   //查看当前登录和过去登录的用户信息 默认读取 /var/log/wtmp 文件
- lastlog   //查看所有⽤户的最后⼀次登录时间
- lastb   //显示所有登陆失败的纪录

## shell脚本

-  第一行固定的格式      #!/bin/sh
-  alias rm="rm -i"   //设置别名   只对当前终端有效
-  `source ~/.bashrc`   设置永久别名
-  unalias 别名 删除别名

