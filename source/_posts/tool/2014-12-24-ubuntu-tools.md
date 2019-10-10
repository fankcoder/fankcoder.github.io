---
layout: post
title: Ubuntu常用
category: 工具
tags: Ubuntu
description: Ubuntu
---

#### 虚拟机安装Ubuntu14.10花屏

右侧Ctrl + F1

再右侧Ctrl + F7
<!-- more-->
#### 常用命令

查找软件

#apt-cache search 正则表达式

查找文件属于哪个包

#dpkg -S filename apt-file search filename

查询软件xxx依赖哪些包

#apt-cache depends xxx

查询软件xxx被哪些包依赖

#apt-cache rdepends xxx

清理旧版本的软件缓存

#sudo apt-get autoclean

清理所有软件缓存

#sudo apt-get clean

删除系统不再使用的孤立软件

#sudo apt-get autoremove

显示当前硬件信息

#lshw

#### 压缩

解压缩 xxx.tar.gz

#tar -zxvf xxx.tar.gz

解压缩 xxx.tar.bz2

#tar -jxvf xxx.tar.bz2

压缩aaa bbb目录为xxx.tar.gz

#tar -zcvf xxx.tar.gz aaa bbb

压缩aaa bbb目录为xxx.tar.bz2

#tar -jcvf xxx.tar.bz2 aaa bbb

解压缩 RAR 文件

1) 先安装

```
#sudo apt-get install rar unrar

#sudo ln -f /usr/bin/rar /usr/bin/unrar
```

2) 解压
#unrar x aaaa.rar

解压缩 ZIP 文件

1) 先安装

```
#sudo apt-get install zip unzip
#sudo ln -f /usr/bin/zip /usr/bin/unzip
```

2) 解压

#unzip x aaaa.zip

#### Install the Latest Pidgin 2.10.9

```
sudo add-apt-repository -y ppa:pidgin-developers/ppa
```

After that, update your system package lists and then you’re able to install packages from the PPA:

```
sudo apt-get update

sudo apt-get install pidgin
```
