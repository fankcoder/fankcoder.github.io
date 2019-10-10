---

layout: post

title: MySQL常用语法

category: 备忘

tags: MySQL

keywords: MySQL

---

  
<!-- more-->
## 常用语法

  

### 登录数据库

mysql -h localhost -uroot -p

  

### 导出数据库

mysqldump -uroot -p db > db.sql

  

### 导入数据库

mysql -uroot -p db < db.sql

// or

mysql -uroot -p db -e "source /path/to/db.sql"

  

### 开启远程登录

grant all privileges on ss.* to 'root'@'%' indentified by 'passoword' with grant option;

// or

update user set Host="%" and User="root"

// 注意%是不包含localhost的

flush privileges;

### 创建用户

CREATE USER 'test'@'localhost' IDENTIFIED BY 'password';

grant all privileges on *.* to test@'localhost' identified by 'test';

### 创建表

CREATE SCHEMA testdb DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;

  

### 赋予数据库权限

  

GRANT ALL ON testdb.* TO 'test'@'localhost';

  
  

### 查看表结构

  

desc 表名;

### 修改表名

  

alter table t_book rename to bbb;

### 添加列：

  

alter table 表名 add column 列名 varchar(30);

### 删除列：

  

alter table 表名 drop column 列名;

  
  

### 清空mysql表中的记录有以下两种方法：

delete from 表名; 不删除id?没实验

truncate table 表名; 删除主键id

  

### 复制表结构

  

create table b select * from users where 0;

create table a like users;

create table b select * from users limit 0;

### 复制表

create table c select * from users;

  

### 删除表名为MyClass 的表

  

drop table MyClass; //删除表名为MyClass 的表

  

### SELECT

  

SELECT * FROM table LIMIT 5;

select * from issu_info limit 7,6; 从第8条开始取,取6条

  
  

SELECT name,LENGTH(name)

FROM world

WHERE LENGTH(name)=5 AND region='Europe'

  

### between

SELECT name, population

FROM world

WHERE population BETWEEN 1000000 AND 1250000

  

### like

SELECT name FROM world

WHERE name LIKE '%a' OR name LIKE '%l'

  

### and

SELECT name, area, population

FROM world

WHERE area > 50000 AND population < 10000000

  

### and not

SELECT name,population,area FROM world

WHERE (area>3000000 or population>250000000) and not (area>3000000 and population>250000000)

  

### in

SELECT name, population/area

FROM world

WHERE name IN ('China', 'Nigeria', 'France', 'Australia')

  

### case when ... then ...else...end

SELECT name,

CASE WHEN continent='Oceania' THEN 'Australasia'

ELSE continent END

FROM world

WHERE name LIKE 'N%'

### 多个when

SELECT name,

CASE WHEN continent in ("Europe","Asia") THEN "Eurasia"

WHEN continent in ("North America","South America","Caribbean") THEN "America" ELSE continent END

FROM world WHERE name LIKE "A%" OR name LIKE "b%"

  

### ORDER BY .. DESC 由大到小排序

SELECT winner,yr,subject FROM nobel

where winner like 'Sir%' ORDER BY yr DESC, winner

  

### 复合查询

SELECT DISTINCT yr

FROM nobel

WHERE subject='Medicine'

AND yr NOT IN(SELECT yr FROM nobel

WHERE subject='Literature')

AND yr NOT IN (SELECT yr FROM nobel

WHERE subject='Peace')

  

### Round

ROUND(7253.86, 0) -> 7254

ROUND(7253.86, 1) -> 7253.9

ROUND(7253.86,-3) -> 7000

  

### CONCAT 将字符连在一起

SELECT CONCAT(region,name)

FROM bbc

  

select name,CONCAT(ROUND(100*population/(select population from world where name = 'Germany'),0),'%') from world

where continent = 'Europe'

  

### ALL

SELECT name

FROM world

WHERE gdp >ALL(SELECT gdp

FROM world

WHERE gdp>0 and continent='Europe')

  

每一个地区中面积最大的国家，引进x,y做对比

SELECT continent, name, area FROM world x

WHERE x.area >= ALL

(SELECT y.area FROM world y

WHERE y.continent=x.continent

AND area>0)

  

列出每个大洲和国家的名字第一个字母顺序排列。

SELECT continent,name FROM world x

WHERE x.name = (SELECT y.name FROM world y

WHERE y.continent= x.continent order by name limit 1

)

  

### 计算函数

  

SUM, COUNT, MAX and AVG

  
  

### distinct去重

  

SELECT DISTINCT region FROM bbc

  

### ORDER BY

  

ASC or DESC for ascending (smallest first, largest last) //小，大

  

### GROUP BY 相同分为一组

  

SELECT continent, COUNT(name)

FROM world

GROUP BY continent

  
  

### having

  

可以筛选成组后的各种数据，where字句在聚合前先筛选记录，也就是说作用在group by和having字句前。而 having子句在聚合后对组记录进行筛选。

  

SELECT continent, SUM(population)

FROM world

GROUP BY continent

HAVING SUM(population)>500000000

  

### JOIN ... ON 多表关联查询

  

SELECT games.yr, city.country

FROM games JOIN city

ON (games.city = city.name)

  

SELECT goal.player,goal.teamid,game.stadium,game.mdate

FROM game JOIN goal ON (game.id=goal.matchid) WHERE goal.teamid = 'GER'

  

### CASE WHEN

  

SELECT name, population

,CASE WHEN population<1000000

THEN 'small'

WHEN population<10000000

THEN 'medium'

ELSE 'large'

END

FROM bbc

  

### LEFT JOIN ... ON ... 可以包含左表空值

  

SELECT games.yr, city.country

FROM games LEFT JOIN city

ON (games.city = city.name)

  

### INSERT

  

INSERT INTO games(yr,city)

VALUES (2012,'London');

  

INSERT SELECT

INSERT INTO games(yr,city)

SELECT yr+12, city FROM games;

  

### UPDATE 更新已经存在的

  

UPDATE games SET city='Paris' WHERE yr = 2004;

  

### DELETE

  

DELETE FROM games WHERE yr=2000;

  

### CREATE并存入

  

CREATE TABLE games

(yr INT NOT NULL PRIMARY KEY

,city VARCHAR(20)

);

INSERT INTO games(yr,city) VALUES (2004,'Athens');

INSERT INTO games(yr,city) VALUES (2008,'Beijing');

INSERT INTO games(yr,city) VALUES (2012,'London');

SELECT * FROM games;

  

### auto-increment 自动存入

  

CREATE TABLE Persons

(

P_Id int NOT NULL AUTO_INCREMENT,

LastName varchar(255) NOT NULL,

FirstName varchar(255),

Address varchar(255),

City varchar(255),

PRIMARY KEY (P_Id)

)

  

### PRIMARY KEY

  

CREATE TABLE Persons

(

Id_P int NOT NULL,

LastName varchar(255) NOT NULL,

FirstName varchar(255),

Address varchar(255),

City varchar(255),

PRIMARY KEY (Id_P)

)

  

### FOREIGN KEY

  

CREATE TABLE Orders

(

Id_O int NOT NULL,

OrderNo int NOT NULL,

Id_P int,

PRIMARY KEY (Id_O),

FOREIGN KEY (Id_P) REFERENCES Persons(Id_P)

)

  

### CREATE INDEX

  

CREATE INDEX index_name

ON table_name (column_name)

在表上创建一个唯一的索引。

CREATE UNIQUE INDEX index_name

ON table_name (column_name)

  

### DROP INDEX

  

ALTER TABLE table_name DROP INDEX index_name

  

### ALTER 修改，增加表的列

  

ALTER TABLE games ADD season VARCHAR(6);

  

### UNIQUE约束

  

CREATE TABLE Persons

(

Id_P int NOT NULL,

LastName varchar(255) NOT NULL,

FirstName varchar(255),

Address varchar(255),

City varchar(255),

UNIQUE (Id_P)

)

  ----
  ### update

 ### 清空表
 
清空mysql表中的记录有以下两种方法：

delete from 表名; 不删除id?没实验

truncate table 表名; 删除主键id

### 导出建表语句

show create table tablename;

### 复制表结构

create table b select * from users where 0; //复制表结构

create table a like users; //复制表结构

create table b select * from users limit 0; //复制表结构

create table c select * from users; //复制表的sql

### 排序

ORDER BY .. DESC 由大到小排序

SELECT winner,yr,subject FROM nobel

where winner like 'Sir%' ORDER BY yr DESC, winner

  

### 复合查询

SELECT DISTINCT yr

FROM nobel

WHERE subject='Medicine'

AND yr NOT IN(SELECT yr FROM nobel

WHERE subject='Literature')

AND yr NOT IN (SELECT yr FROM nobel

WHERE subject='Peace')

  

### Round

ROUND(7253.86, 0) -> 7254

ROUND(7253.86, 1) -> 7253.9

ROUND(7253.86,-3) -> 7000

  

### CONCAT 将字符连在一起

SELECT CONCAT(region,name)

FROM bbc

  

select name,CONCAT(ROUND(100*population/(select population from world where name = 'Germany'),0),'%') from world

where continent = 'Europe'

distinct去重

SELECT DISTINCT region FROM bbc

### having

having 可以让我们筛选成组后的各种数据，where字句在聚合前先筛选记录，也就是说作用在group by和having字句前。而 having子句在聚合后对组记录进行筛选。

SELECT continent, SUM(population)

FROM world

GROUP BY continent

HAVING SUM(population)>500000000

  

### JOIN ... ON 多表关联查询

SELECT games.yr, city.country

FROM games JOIN city

ON (games.city = city.name)


### mysql设置权限命令

GRANT ALL PRIVILEGES ON *.* TO ‘root’@‘%’ IDENTIFIED BY ‘root’ WITH GRANT OPTION;

### flush privileges;刷新权限

  

### 创建聚合索引

CREATE INDEX index_name

ON table_name ( column1, column2,...);
  

### 查询索引
SHOW INDEX FROM table_name