---
layout: post
title: Ubuntu&OSX的app抓包方案
category: 技术
tags: app, 抓包
keywords: 爬虫
---

## Ubuntu抓包方案

## 必备条件
电脑手机同一wifi (如果pc做了ip端口映射也可以使用广域网ip抓包)

## 方案1

### Fiddler

Fiddler在Ubuntu上基于mono运行

Fiddler & mono 下载地址

```
http://fiddler.wikidot.com/mono
```

1. 下载并装成功 
2. 打开Fiddler-> tools->options -> connections  查看Port选项，记录下来
3. 手机连接wifi并选择代理，方式手动代理
4. 填写主机名为PC本地ip地址，端口填写刚刚记录的Port
5. 打开手机app，观察Fiddler接受\发送的请求

## 方案2

### Charles

1. 下载安装
```
https://www.charlesproxy.com/download/
```
2. 打开charles在Proxy->ProxySetting设置一个端口号，并勾选Enable
3. 设置手机代理
4. 如果手机包出现乱码情况，请安装charles证书（安装方法在最后）。如果手机无法安装.pem修改为.crt
（小米手机进入到设置-wifi，点击高级，安装证书）

5. 打开手机app，观察charles接受\发送的请求


## mac osx抓包方案

###  Charles

使用手机安装SSL证书

在help->SSL Proxying-> "nstall Charles Root Certificate on a Mobile Device or remote Browser"

然后根据提示访问安装