---
layout: post
title: Linux 常用资源
category: 备忘
tags: Linux
keywords: Linux
description: 
---


## 常用指令，持续更新。。

### xdg-open

xdg-open opens a file or URL in the user's preferred application. 

### 遍历创建

    mkdir -p 

### 当前目录

    pwd 

### 上次目录

    cd -

### 删除目录 -f强制

    rm -rf

### 复制目录 -a与源文件一模一样

    cp -r

### 剪切/改名

    mv

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
