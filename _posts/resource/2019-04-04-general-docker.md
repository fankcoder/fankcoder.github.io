---
layout: post
title: Docker 笔记
category: 备忘
tags: docker
keywords: docker
description: 
---

## 基础类
### 查看docker信息
### # 查看docker版本
docker version
#### 显示docker系统的信息
docker info
#### 日志信息
docker logs
#### 故障检查
service docker status
#### 启动关闭docker
sudo service docker start|stop

## 查看容器信息
#### 查看当前运行的容器
docker ps
#### 查看全部容器
docker ps -a
#### 查看全部容器的id和信息
docker ps -a -q
#### 查看全部容器占用的空间
docker ps -as
#### 查看一个正在运行容器进程，支持 ps 命令参数
docker top

## 启动停止容器等操作
docker start|stop|restart [id]
#### 暂停|恢复 某一容器的所有进程
docker pause|unpause [id]
#### 杀死一个或多个指定容器进程
docker kill -s KILL [id]
#### 停止全部运行的容器
docker stop `docker ps -q`
#### 杀掉全部运行的容器
docker kill -s KILL `docker ps -q`

## 交互式进入容器
sudo docker exec -it {{containerName or containerID}} bash