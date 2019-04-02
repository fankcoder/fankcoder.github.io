---
layout: post
title: 第N次折腾vps科学上网，解决方案笔记
category: 工具
tags: ss, google
keywords: ss
---

# 背景
## 过去
记得第一打开谷歌，是在初中的某一天，和当时的我熟悉的百度比较，除了它的Logo比较新颖之外，印象最深的就是这个网站，与国内所有的搜索引擎不同，它不能在线听歌，那时候正是国内盗版软件光盘盛行的时候，关心音乐的版权更是极少的。

## 后来
有一天在大学机房做实验，老师闲聊说谷歌以后不能访问了，我听过也没太在意，因为在我意识中互联网世界就是同一个虚拟世界，可惜从那以后对我来说它就不再是了。之后的事大家都知道了，百度虚假广告越来越多，在当时我有很长一段时间都觉得，使用搜索引擎是一件极其痛苦的事，每当我在百度查找需要的东西，我大概需要过滤掉80%无用的信息，其中30%是广告，30%是色情信息，20%是无关信息。于是我将系统hosts替换，重新使用起了谷歌。

## 再后来
替换hosts这个方法我大概使用了2，3年之久（首先因为免费，其次它比某些收费vpn访问速度还快，缺点是不能看youtube视频）
这时候我已经是一名谷歌重度使用者了。
在2015年7月10号，那段时间hosts经常不稳定，找到了一篇用vps搭建ss的教程，在搬瓦工购入了一台vps，从此开始了vps搭建ss的折腾之旅

# 目前的完美折腾方案
由于我的vps购入比较早，19.9$/Y（现在应该买不到了）它还是古老的OVZ架构，不能享用bbr加速方案。

### 已一台刚装好的centOS6.5为例

安装git&ss

```
sudo apt install git
pip install https://github.com/shadowsocks/shadowsocks/archive/master.zip -U
```

配置ss

```
vim /etc/shadowdowsocks.json
```

配置ss例子，端口写不常用的，并且的越随意越好，记得不要超过65535

```
{                                  
    "server":"my_server_ip",          //服务器的IP地址
    "server_port":7711,              //服务器的端口，
    "local_address": "127.0.0.1",       //本机IP地址
    "local_port":1080,               //本机端口
    "password":"mypassword",        //自己设定的密码
    "timeout":600,                  //超出时间
    "method":"chacha20-ietf",          //加密方法，推荐使用"aes-256-cfb"
    "fast_open": false                //true 或 false}
}

# 若是多用户模式，将server_port和password合并为port_password：
"port_password": {
         "443": " mypassword 1”,  //对应端口设定不同的密码
         "8888": " mypassword 2”
     },
```

安装gevent，如果使用Python2，高版本的gevent可能会安装失败，官方推荐1.1版本

```
$ yum install -y libevent
$ pip install greenlet
$ pip install gevent==1.1
```

强烈使用推荐的chacha20-ietf算法。安装新算法需要先安装libsodium

直接安装or编译安装随心情

```
yum install epel-release
yum install libsodium
```
or
```
yum -y groupinstall "Development Tools"
wget https://github.com/jedisct1/libsodium/releases/download/1.0.15/libsodium-1.0.15.tar.gz
tar xf libsodium-1.0.15.tar.gz && cd libsodium-1.0.15
./configure && make -j2 && make install
echo /usr/local/lib > /etc/ld.so.conf.d/usr_local_lib.conf
ldconfig
```

启动ss服务

```
ssserver -c /etc/shadowsocks.json
# 若想让ss一直在后台运行，可运行：
nohup ssserver -c /etc/shadowsocks.json > /dev/null 2>&1 &
```

客户端

macos: ShadowsocksX-NG
win: https://shadowsocks.org/en/download/clients.html
android: 可以先从pc下个apk传到手机（或者免费vpn有很多临时用下不错）再用gg play更新/下载
ios:wingy

## kcptun

#### 参考文档
https://github.com/easonhuang123/blog/issues/1
https://github.com/shadowsocksrr/shadowsocks-rss/wiki/libsodium
下面是一篇升级glibc的文章，我的确升级成功了，但是bbr还是用不了，放弃了
https://cnodejs.org/topic/56dc21f1502596633dc2c3dc