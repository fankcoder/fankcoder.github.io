---
layout: post
title: MySQL分区
category: 技术
tags: SQL
keywords: SQL
description: MySQL从5.1版本添加了分区(Partition)支持。 
---

MySQL从5.1版本添加了分区(Partition)支持。MySQL支持的分区为水平分区（将同一张表中的不同行记录分配到不同的物理文件中）

**分区和手动分表对比**

|手动分表|分区|
|:--------:| -------------:|
| 多张数据表 | 一张数据表|
| 重复数据的风险 | 没有数据重复的风险 |
写入多张表|写入一张表
没有统一的约束限制|强制的约束限制

### 分区类型

#### RANGE分区

行数据 基于属于一个给定连续区间的列值被放入分区。例如日期列

```
CREATE TABLE sales(
money INT UNSIGNED NOT NULL,
date DATETIME
)ENGINE=INNODB
PARTITION by RANGE (YEAR(date)) (
PARTITION p2018 VALUE LESS THEN (2019),
PARTITION p2019 VALUE LESS THEN (2020)
);
```

```
alter table sales drop partition p2018;
```

#### LIST分区

和RANGE类似，只是LIST分区面向的是离散的值。

#### HASH分区

根据用户自定义的表达式的返回值来进行分区，返回值不能为负数。

#### KEY分区

根据MySQL数据库提供的哈希函数来进行分区。

#### COLUMNS分区
RANGE 和 LIST的进化，支持多列分区


### 查看分区

```
SHOW VARIABLES LIKE '%partition%'
```
or

```
SHOW PLUGINS
```

### 查看每个分区的具体信息

```
SELECT * FROM information_schema.PARTITIONS
WHERE table_schema=database() AND table_name='t'
```

### 分区和性能

并不是分区了就能带来性能提升，分区前必须充分了解分区的使用环境。

数据库的应用分为两类：一类是OLTP(在线事物处理)，如Blog、电子商务、网络游戏等；另一类是OLAP(在线分析处理)，如数据仓库，数据集市。

对于OLAP的应用，分区是可以很好的提高查询性能。

对于OLTP的应用，分区应该非常小心。这种情况获取数据一般都是通过索引返回几条记录即可。而根据B+树索引原理可知，对于一张大表，一般的B+树需要2~3次的磁盘IO。因此B+树可以很好的 完成操作，不需要分区的帮助，设计不好的分区会带来严重的性能问题。