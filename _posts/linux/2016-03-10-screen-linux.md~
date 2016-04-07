---
layout: post
title: screen工具
category: linux
tags: screen
keywords: screen
description: 使用screen管理会话
---
## screen 多窗口工具
远程到服务器，通常情况下我们会为每一个长时间任务开一个远程终端窗口，操作下载东西或者Print十几万行数据，等待，是一件让人非常痛苦的事情，而且在此期间不能关掉窗口或者断开连接，否则这个任务就会被杀掉了，screen把我们从痛苦中解救出来。

screen是一个可以在多个进程之间多路复用一个物理终端的窗口管理器。screen中有会话的概念，用户可以在一个screen会话中创建多个screen窗口，在每一个screen窗口中就像操作一个真实的telnet/SSH连接窗口那样。当断开或关闭终端时，只要不杀死screen的进程，待重新连接后任能继续断开前的操作。

## screen命令
查看会话：

```
### screen -ls
```

There is a screen on:
19393.test      (Detached)
1 Socket in /var/run/screen/S-root.

选择会话

```
### screen -r test
```

或者

```
### screen -r 19393
```

创建一个新会话

```
### screen -dmS test
```

## 快捷键

C-a d 暂时断开screen会话

C-a ?	显示所有键绑定信息

C-a w	显示所有窗口列表

C-a C-a	切换到之前显示的窗口

C-a c	创建一个新的运行shell的窗口并切换到该窗口

C-a n	切换到下一个窗口

C-a p	切换到前一个窗口(与C-a n相对)

C-a 0..9	切换到窗口0..9

C-a a	发送 C-a到当前窗口

C-a k	杀掉当前窗口

C-a [	进入拷贝/回滚模式
