---
layout: post
title: MySQL主从备份实战
category: 技术
tags: SQL
keywords: 表设计
---

## 测试平台
主：mysql  Ver 8.0.13 for osx10.14 on x86_64 (Homebrew)
从：mysql Ver 8.0.17 for windows10

## 两台机器分别安装
mysql-server mysql-client

## 先保证两台机器能互相连接到对方数据库
通过命令或者图形工具测试。
```
mysql -u USERNAME -p PASSWORD -h HOSTNAMEORIP DATABASENAME 
```

如果不能直接连接，最好添加除root之外的用户，然后授予权限，添加和授权方法在"给从库授权"处。
需要说明的是windows用户记得关闭防火墙。

## 配置主数据库

#### 修改配置文件

```
sudo vim /etc/mysql/my.cnf
```

ip地址自行更换
```
bind-address            = 192.168.X.X
server-id               = 1 
log_bin                 = /var/log/mysql/mysql-bin.log
binlog_do_db            = testshop
```

#### 重启MySQL服务

```
sudo service mysql restart
```

#### 给从库授权

自行替换user,password
```
GRANT REPLICATION SLAVE ON *.* TO 'slave_user'@'%' IDENTIFIED BY 'password';
```

如果授权失败，输入以下命令，添加用户，之后再授权，记得用户名密码要统一

```
mysql> CREATE USER 'monty'@'localhost' IDENTIFIED BY 'some_pass';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'monty'@'localhost' WITH GRANT OPTION;
mysql> CREATE USER 'monty'@'%' IDENTIFIED BY 'some_pass';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'monty'@'%' WITH GRANT OPTION;
```

#### 更新权限

```
FLUSH PRIVILEGES;
```

#### 表加锁

如果不能保证在配置过程中表不会被修改，建议给表加锁，防止同步出现错误。

**加锁**
```
USE newdatabase;
FLUSH TABLES WITH READ LOCK;
```

**记录主库二进制日志文件**
记录File名和Positiion位置，从库配置需要用到。
```
mysql> SHOW MASTER STATUS;
+------------------+----------+--------------+------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+------------------+----------+--------------+------------------+
| mysql-bin.000001 |      107 | newdatabase  |                  |
+------------------+----------+--------------+------------------+
1 row in set (0.00 sec)
```

**导出主库数据**
```
mysqldump -u root -p --opt newdatabase > newdatabase.sql
```

**解锁**
```
UNLOCK TABLES;
QUIT;
```

## 配置从数据库

#### 创建数据库，并导入数据文件
使用命令行或者图形工具导入都可以，需要注意在创建库时，最好将字符编码统一。

```
CREATE DATABASE newdatabase;
EXIT;
```
**导入**
```
mysql -u root -p newdatabase < /path/to/newdatabase.sql
```

#### 修改配置文件

打开
```
sudo vim /etc/mysql/my.cnf
```

windows下自行找到MySQL安装位置，在安装位置根目录下创建my.ini文件修改。

```
server-id               = 2
relay-log               = /var/log/mysql/mysql-relay-bin.log
log_bin                 = /var/log/mysql/mysql-bin.log
binlog_do_db            = testshop
```

#### 重启从库服务
```
sudo service mysql restart
```

#### 从库配置同步
用户密码为主库的，二进制日志文件和日志位置，由主库输入"""SHOW MASTER STATUS;"""得到。
```
CHANGE MASTER TO MASTER_HOST='192.168.x.x',MASTER_USER='master_user!', MASTER_PASSWORD='password', MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=  107;
```

#### 开启从库同步
```
START SLAVE;
```

#### 查看从库状态,是否连接成功等信息
```
SHOW SLAVE STATUS;
```

至此，主从同步配置完成，修改主库数据试试吧，如果从库数据已经同步成功，恭喜你~