---
layout: post
title: Django基本命令
category: 备忘
tags: django
description: Django基本命令
---

### 新建一个 django project

     django-admin.py startproject project-name

### 新建 app

     python manage.py startapp app-name
     #或
     django-admin.py startapp app-name

### 同步数据库

     python manage.py syncdb 
     #注意：Django 1.7.1及以上的版本需要用以下命令
     python manage.py makemigrations
     python manage.py migrate

这种方法可以创建表，当你在models.py中新增了类时，运行它就可以自动在数据库中创建表了，不用手动创建。

备注：对已有的 models 进行修改，Django 1.7之前的版本的Django都是无法自动更改表结构的，不过有第三方工具 south,详见 Django 数据库迁移 

### 使用开发服务器

开发服务器，即开发时使用，一般修改代码后会自动重启，方便调试和开发，但是由于性能问题，建议只用来测试，不要用在生产环境。

     python manage.py runserver
     python manage.py runserver 8001
     python manage.py runserver 0.0.0.0:8000
     # 如果是外网或者局域网电脑上可以用其它电脑查看开发服务器

### 清空数据库

     python manage.py flush

此命令会询问是 yes 还是 no, 选择 yes 会把数据全部清空掉，只留下空表。

### 创建超级管理员

     python manage.py createsuperuser
  
     # 按照提示输入用户名和对应的密码就好了邮箱可以留空，用户名和密码必填
  
     # 修改 用户密码可以用：
     python manage.py changepassword username

### 导出数据 导入数据

     python manage.py dumpdata appname > appname.json
     python manage.py loaddata appname.json

### Django 项目环境终端

     python manage.py shell

如果你安装了 bpython 或 ipython 会自动用它们的界面，推荐安装 bpython。

这个命令和 直接运行 python 或 bpython 进入 shell 的区别是：你可以在这个 shell 里面调用当前项目的 models.py 中的 API，对于操作数据，还有一些小测试非常方便。

### 数据库命令行

     python manage.py dbshell

Django 会自动进入在settings.py中设置的数据库，如果是 MySQL 或 postgreSQL,会要求输入数据库用户密码。
在这个终端可以执行数据库的SQL语句。如果您对SQL比较熟悉，可能喜欢这种方式。

### 更多命令

     终端上输入 python manage.py 可以看到详细的列表，在忘记子名称的时候特别有用。




