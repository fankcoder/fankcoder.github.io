---
layout: post
title: Linux 笔记
category: 备忘
tags: Linux
keywords: Linux
description: 
---


## 常用指令，持续更新。。

### 自用.vimrc服务器无插件版

```
 set autoindent
 set tabstop=4
 set shiftwidth=4
 set expandtab
 set number
 ab xdate <c-r>=strftime("20%y%m%d %H:%M:%S")<C-I>
 set nu
 colorscheme slate
 syntax on
```

### Nginx官方的Yum源

vim /etc/yum.repos.d/nginx.repo

[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=0
enabled=1

### xdg-open

xdg-open opens a file or URL in the user's preferred application. 

### find搜索

    find /root -iname install.log  #-i不区分大小写
    find /root -user root  #按照所有者搜索
    find /var/log/ -mtime +10 #查找10天前修改的文件
    -10 10天内修改文件
    10 10天当天修改的文件
    +10 10天前修改的文件
    atime 文件访问时间
    ctime 改变文件属性
    mtime 修改文件内容

    find . -size 25k
    +25k 大于25k
    -小于25k
    M大写

    find /etc -size +20k -a -size -50k
    -a and 和
    -o or 或
    
    -exec ls -lh {} \;
    find /etc -size +20k -a -size -50k -exec ls -lh {} \;
    将第一个结果放入第二个在处理一遍
    
###  grep
grep是包含匹配搜索文件内容,find是完全匹配搜索文件名

    grep "size" anaconda-ks.cfg
    -v 取返
    -i 不分大小写
    
### 帮助命令

		man -f 查看级别相当于whatis
		whereis passwd
		ls --help选项帮助命令
		info 命令

### 常用压缩格式 .zip .gz .bz2 .tar.gz .tar.bz2
    zip 压缩文件名 源文件 #压缩文件
    zip -r 压缩文件名 源文件 #压缩目录

    unzip 压缩文件 #解压缩

    gzip 源文件 #压缩为.gz的文件,源文件会消失
    gzip -c 源文件 > 压缩文件 #源文件保留
    gzip -r 目录 #压缩目录下所有子文件,不压缩目录
    gzip -d 压缩文件 #解压
    gunzip 压缩文件 #解压

    打包tar
    tar -cvf 打包文件名 源文件
    tar -xvf 解打包
    tar -zcvf 压缩包名.tar.gz 源文件
    tar -zxvf 压缩包名.tar.gz #解压缩

### shutdown [选项] 时间
    -c 取消前一个关机命令
    -h 关机
    -r 重启
    rboot  重启

### mount -a 自动挂载/etc/fatab

### w 
	查看用户登录信息 who类似

### last 
	所有用户登录信息 /var/log/wtmp

### lastlog 
	最后登录时间
    

#### 查看文件夹占用量

    du -smh .
    
### 查看磁盘

    sudo fdisk -l
    
#### 挂载ntfs

    sudo mount -t ntfs-3g /dev/sdb1 /media/userom/

#### rsync远程同步工具
rsync是linux系统下的数据镜像备份工具。使用快速增量备份工具Remote Sync可以远程同步，支持本地复制，或者与其他SSH、rsync主机同步。

    rsync -av 源目录 目标目录
    

#### 正则查找文件

    sudo find ~/ -name “*.abc”

#### 查看进程

    ps aux | grep process

#### 搜索加删除

    find ./ -name "*.md~" -exec rm -rf {} \;
    
#### 定时任务

    crontab
    
#### 后台管理工具

    screen

#### 查询所有文件及子目录文件个数

    sudo find . -type f -print | wc -l    

### 诊断网络

    mtr 
    ping
    traceroute
    dig

### 查看本地网络服务活动状态

    lsof -i

### 查看自己的外网ip

    curl ifconfig.me

### 登陆到其他用户

    login

### 查看端口的占用

    lsof -i:8087  查看8087端口的使用

### 批量杀死进程

    ps -aux|grep name|grep -v grep|cut -c 9-15|xargs kill -9

### 查看当前时间

    date       时间
    date +%s   时间戳
    date -d "2010-07-20 10:25:30" +%s  指定时间时间戳
    date -d "@1279592730"    时间戳转时间
    date -d "1970-01-01 14781 days" "+%Y/%m/%d %H:%M:%S" 

### 查看进程内存使用情况

    top -d 1 -p pid [,pid ...]
    pmap pid 
    ps aux|grep process_name
    查看/proc/process_id/文件夹下的status文件

### 查看Linux内核版本或发布版本

    lsb_release -a
    uname -a

### 列出本机监听的端口号

    netstat –tlnp
    netstat -anop

### 在远程机器上运行一段脚本

    ssh user@server bash < /path/to/local/script.sh

### 端口扫描

    nc -z -v -n 127.0.0.1 20-100

### 负载测试，30秒内向Google发起20个并发连接

    siege -c20 www.google.co.uk -b -t30s