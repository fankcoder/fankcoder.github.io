---
layout: post
title: 数据库设计
category: 技术
tags: SQL
keywords: 表设计
---
## 设计步骤

1. 需求分析
2. 逻辑设计 
3. 物理设计
4. 维护优化

### 需求分析

1. 数据是什么
2. 数据有哪些属性
3. 数据和属性各自的特点有哪些

#### 为什么要进行需求分析

1. 了解系统中所要存储的数据
2. 了解数据的存储特点。如：时效性数据（过期清理）
3. 了解数据的生命周期。（数据量很大但不是核心数据，分库分表来存储）如：日志数据

#### 确定需求

1. 实体和实体之间的关系（1对1，1对多，多对多）
2. 实体包含的属性?
3. 哪些属性或属性的组合可以唯一标识一个实体
<!-- more-->
#### 举例

一个小型电子商务网站的几个核心模块：用户模块，商品模块，订单模块，购物车模块，供应商模块。

**用户模块**
包含属性：用户名，密码，电话，邮箱，身份证号，地址，姓名，昵称
可选唯一标识属性：用户名，身份证，电话
存储特点：随系统上线时间逐渐增加，需要永久存储

**商品模块**
用于记录网站中所销售的商品信息
包含属性：商品编码，商品名称，商品描述，商品类别，供应商名称，重量，有效期，价格。。。
可选唯一标识属性：(商品名称，供应商名称)，商品编码
存储特点：对于下线商品可以归档存储(如果不再供应，将数据迁移至其他表)

**订单模块**
用于用户订购商品的信息
包括属性：订单号，用户姓名，电话，收获地址，商品编号，商品名称，数量，价格，订单状态，支付状态，订单类型。。。
可选唯一标识属性：订单号
存储特点：永久存储（分表，分库存储）

**购物车模块**
用于保持用户购物时选择的商品
包括属性：用户名，商品编号，商品名称，价格，数量，商品描述，商品分类，加入时间。。。
可选唯一标识：（用户名，商品编号，加入时间），（购物车编号）
存储特点：不用永久存储（设置归档，清理规则）

**供应商模块**
用于保存所销售商品的供应商信息
包括属性：供应商编号，名称，联系人，电话，营业执照，地址，法人。。。
可选唯一标识：(供应商编号)，（营业执照编号）
存储特点：永久存储

**对应关系**
商品和供应商--多对多
商品和订单--多对多
商品和购物车--多对多

用户和订单--一对多
用户和购物车--一对多

### 逻辑设计

使用ER图对数据库进行逻辑建模

#### 作用

1. 将需求转化为数据库的逻辑模型
2. 通过ER图的型式模型对逻辑模型进行展示
3. 同所选用的具体的DBMS系统无关

#### ER图名词

关系：一个关系对应一张表
元组：表中的一行为一个元组
属性：表中的一列为一个属性，列名为属性名
候选码：表中的某个属性组，它可以唯一确定一个元组
主码：一个关系有多个候选码，选定其中一个为主码
域：属性的取值范围
分量：元组中的一个属性值

**ER图形表示**
矩形：表示实体集
菱形：联系集（将多对多的关系转换为一对多的关系）
椭圆：实体的属性
线段：将属性连接到实体集，或将实体集连接到联系集


#### 设计范式
常见数据库设计范式包括：

第一范式（1NF）、第二范式（2NF）、第三范式（3NF）、巴斯-科德范式（BCNF）、第四范式(4NF）和第五范式（5NF，又称完美范式）满足最低要求的范式是第一范式（1NF）。在第一范式的基础上进一步满足更多规范要求的称为第二范式（2NF），其余范式以次类推。一般说来，数据库只需满足第三范式(3NF）就行了

三范式
1NF:数据表中的所有字段都是单一属性，字段不可再分; 
2NF:数据库的表中不存在非关键字段对任一候选关键字段的部分函数依赖;（所有单关键字段的表都符合2NF） 
3NF: 非主键字段不能相互依赖; 

解释: 
1NF:原子性 字段不可再分,要求数据表都是二维表; 
2NF:部分函数依赖是指存在组合关键字中的某一关键字决定非关键字的情况; 会出现操作异常
3NF:每列都与主键有直接关系，不存在传递依赖;

BC范式 BCNF
数据库表中如果不存在任何字段对任一候选关键字段的传递函数依赖则符合BC范式。
解释：如果是符合关键字，则复合关键字之间也不能存在函数依赖关系。

#### 操作异常
插入异常：如果某实体随另一个实体的存在而存在，即缺少了某个实体时无法表示这个实体，那么表就存在插入异常
更新异常：如果更改表所对应的某个实例的单独属性时，需要将多行进行更新，那么表存在更新异常
删除异常：如果删除表的某一行来反应某实体实例失效时导致另一个不同实体实例信息丢失，那么这个表就存在删除异常


### 物理设计

根据逻辑设计转化为物理设计

1. 选择合适的数据库
2. 定义数据库，表及字段的命名规范
3. 选择合适的字段类型
4. 反范式化

#### 命名规则

1. 可读性原则
2. 表意性原则
3. 长名原则（简写会有歧义）

#### 字段类型选择

当一个列可以选择多种数据类型时，应该优先考虑数字类型，其次是日期或二进制类型，最后是字符型。对于相同级别的数据类型，应该优先选择占用空间小的数据类型。

##### 数字选择原则：
1. 查询条件，join条件及排序操作时，数字比字符串快。
2. 在数据库中，数据处理以页为单位，列的长度越小，利于性能提升。

##### char与varchar选择原则

1. 如果列中药存储的数据长度差不多是一致的，则应该考虑用char;否则应该考虑用varchar。（电话，身份证号）
2. 如果列中的最大数据长度小于50Byte，则一般也考虑用char。（该列用的少也可以考虑varchar）
3. 一般不宜定义大于50Byte的char类型列（utf8 50/3 15个字符）

##### decimal与float

1. decimal用于存储精确数据，而float只能用于存储非精确 数据。故精确数据只能选择用decimal类型。
2. 由于float的存储空间开销一般比decimal小，故非精确数据优先选择float类型(精确到7位小数需要4字节，15位小数只需要8字节)

##### 日期时间类型

int4字节只能存到2038-1-19 11:14:07即2^32=2147483648


#### 其他注意事项

#### 如何选择主键
##### 区分业务主键和数据库主键

业务主键用于标识业务数据，进行表与表之间的关联；
数据库主键为了优化数据存储（innodb会生成6个字节的隐含主键）

##### 根据数据库的类型，考虑主键是否要顺序增长

有些数据库是按主键的顺序逻辑存储的

##### 主键的字段类型所占空间要尽可能的小

对于使用聚集索引方式存储的表，每个索引后都会附加主键信息。

#### 避免使用外键约束

1. 降低数据导入的效率
2. 增加运维成本
3. 虽然不建议使用外键约束，但是相关联的列上一定要建立索引

#### 避免使用触发器

1. 降低数据导入的效率
2. 可能会出现意想不到的数据异常
3. 使业务逻辑变的复杂

#### 关于预留字段

1. 无法准确知道预留字段的类型
2. 无法准确知道预留字段中所存储的内容
3. 后期维护预留字段所需要的成本，同增加一个字段所需要的成本是相同的（即使是改名字也会重新建列）
4. 严禁使用预留字段


#### 反范式化的表建立

允许存在少量的数据冗余，反范式化主要是使用空间来换取时间。高并发项目中经常使用这种技巧。

好处：
1. 减少表的关联数量
2. 增加数据的读取效率(一般读写比约为3：1，4：1)
3. 反范式化一定要适度

### 维护优化

#### 维护数据字典（状态字段，含义字典）

1. 第三方工具
2. 利用数据库的备注字段
3. 导出数据字典


```
SELECT
  a.TABLE_NAME 表名,
  b.TABLE_COMMENT 表备注,
  a.COLUMN_NAME 列名,
  a.COLUMN_TYPE 数据类型,
  a.DATA_TYPE 字段类型,
  a.CHARACTER_MAXIMUM_LENGTH 长度,
  a.IS_NULLABLE 是否为空,
  a.COLUMN_DEFAULT 默认值,
  a.COLUMN_COMMENT 备注 
FROM
INFORMATION_SCHEMA.COLUMNS a,information_schema.TABLES b
where
-- 单行注释，--后必须有一个空格
/* 多行注释
   table_schema--数据库名 
   INFORMATION_SCHEMA.COLUMNS-- mysql的所有字段的表
   information_schema.TABLES -- mysql的所有表的描述
*/
a.table_schema ='gatherinfo'    
and a.TABLE_NAME=b.TABLE_NAME
order by a.TABLE_NAME;
```

#### 索引优化

1. 出现在where从句，group by从句，Order by从句中的列
2. 可选择性高的列放到索引前面（关于索引列的顺序和where后面的字段顺序保持一致，现在所有的sql执行前会在sql优化器中编译，优化器会按照索引和统计信息重新排序优化）
3. 索引中不要包括太长的数据类型（太长数据可以只对前16个字节进行索引，或者md5编码查md5）

##### 索引注意事项
1. 并不是越多越好，过多的索引不但会降低写的效率，也会降低读的效率（优化器选择的慢）
2. 定期维护索引碎片
3. 在sql语句中不要使用强制索引关键字

#### 维护表结构

注意事项
1. 使用在线变更表结构工具
MySQL5.5之前可以使用pt-online-schema-change
MySQL5.6之后本身支持在线表结构的变更
2. 同时对数据字典进行维护
3. 控制表的宽度和大小

#### 数据库适合的操作

1. 批量操作。逐条操作适合在程序中完成
2. 禁止使用 select * 这样的查询（io浪费）
3. 控制使用用户自定义函数（对索引影响）
4. 不要使用数据库中的全文索引（额外建立索引文件，对中文支持不好）

#### 在适当的时候对表进行水平拆分或垂直拆分

##### 垂直拆分
为了控制表的宽度

1. 经常一起查询的列放在一起
2. text,blob等大字段拆分出到附加表中

##### 水平拆分
通过主键哈希方式来拆分，按主键的模取值，平均拆分（id%4+1 = [1,2,3,4]）