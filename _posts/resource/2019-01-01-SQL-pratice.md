---
layout: post
title: SQL练习
category: 备忘
tags: SQL
keywords: SQL
description: 
---
#### 查找最晚入职员工的所有信息
    
    SELECT * FROM employees ORDER BY hire_date DESC LIMIT 0,1;
    或
    SELECT * FROM employees WHERE hire_date = (SELECT       MAX(hire_date) FROM employees);
    
区别在于当存在重复日期，下面会输出多个

#### 两张表查找所有已经分配部门的员工的last_name和first_name

    select e.last_name, e.first_name, d.dept_no from employees e join dept_emp d on e.emp_no = d.emp_no
    
#### 查找所有员工的last_name和first_name以及对应部门编号dept_no，也包括展示没有分配具体部门的员工
    
    select e.last_name, e.first_name, d.dept_no from employees e left join dept_emp d on e.emp_no == d.emp_no;


#### Group和having

    WHERE语句在GROUP BY语句之前；SQL会在分组之前计算WHERE语句。   
    HAVING语句在GROUP BY语句之后；SQL会在分组之后计算HAVING语句。
    
    如：找出所有员工当前(to_date='9999-01-01')具体的薪水salary情况，对于相同的薪水只显示一次,并按照逆序显示
    select salary from salaries where to_date='9999-01-01' group by salary order by salary desc;

#### 查找薪水涨幅超过15次的员工号emp_no以及其对应的涨幅次数t

    select emp_no, count(salary) as t from salaries group by emp_no having t >15

#### 获取所有非manager的员工emp_no
    
    select e.emp_no from employees e left join dept_manager d on e.emp_no = d.emp_no where d.emp_no is Null;
    
#### 获取所有员工当前的manager，如果当前的manager是自己的话结果不显示，当前表示to_date='9999-01-01'。
    select e.emp_no, m.emp_no as manager_no from dept_emp e 
    inner join dept_manager m on e.dept_no = m.dept_no
    where m.to_date='9999-01-01' and e.to_date='9999-01-01' and m.emp_no <> e.emp_no
    
#### distinct

MySQL发现第1个匹配行后，停止为当前的行组合搜索更多的行。

    从titles表获取按照title进行分组，每组个数大于等于2，给出title以及对应的数目t。注意对于重复的emp_no进行忽略。
    
    select title, count(distinct emp_no) t from titles group by title having title >2;
    
#### 查找employees表所有emp_no为奇数，且last_name不为Mary的员工信息，并按照hire_date逆序排列
    
    select * from employees where emp_no %2 = 1 and last_name != 'Mary' order by hire_date desc;
    
#### 查找当前薪水(to_date='9999-01-01')排名第二多的员工编号emp_no、薪水salary、last_name以及first_name，不准使用order by

    select e.emp_no, max(s.salary), e.last_name, e.first_name 
    from employees e , salaries s 
    where e.emp_no = s.emp_no and s.to_date='9999-01-01' and
    s.salary < (select max(salary) from salaries)
    
#### 三表左连接
    查找所有员工的last_name和first_name以及对应的dept_name，也包括暂时没有分配部门的员工
    
    select e.last_name, e.first_name, d.dept_name from employees e left join dept_emp de on e.emp_no = de.emp_no left join departments d on de.dept_no = d.dept_no

    
#### 嵌套运算
    查找员工编号emp_no为10001其自入职以来的薪水salary涨幅值growth
    
    select (
    (select salary from salaries where emp_no=10001 order by to_date desc limit 0,1) - (select salary from salaries where emp_no=10001 order by to_date asc limit 0,1)
     )as growth

#### 构建新表查询
    查找所有员工自入职以来的薪水涨幅情况，给出员工编号emp_no以及其对应的薪水涨幅growth，并按照growth进行升序
    select now_s.emp_no, (now_s.salary - old_s.salary) as growth 
    from (select s.emp_no, s.salary  from employees e inner join salaries s on s.emp_no = e.emp_no where s.to_date='9999-01-01') as now_s
    inner join 
    (select s.emp_no, s.salary  from employees e inner join salaries s on s.emp_no = e.emp_no where s.from_date=e.hire_date) as old_s
    on now_s.emp_no = old_s.emp_no
    order by growth;
    
#### not in ; join on and 

    对所有员工的当前(to_date='9999-01-01')薪水按照salary进行按照1-N的排名，相同salary并列且按照emp_no升序排列
    SELECT s1.emp_no, s1.salary, COUNT(DISTINCT s2.salary) AS rank FROM salaries AS s1, salaries AS s2 WHERE s1.to_date = '9999-01-01'  AND s2.to_date = '9999-01-01' AND s1.salary <= s2.salary GROUP BY s1.emp_no ORDER BY s1.salary DESC, s1.emp_no ASC
    
#### 获取员工其当前的薪水比其manager当前薪水还高的相关信息，当前表示to_date='9999-01-01',

    结果第一列给出员工的emp_no，
    第二列给出其manager的manager_no，
    第三列给出该员工当前的薪水emp_salary,
    第四列给该员工对应的manager当前的薪水manager_salary    
    
    select de_s.emp_no, dm_s.emp_no as manager_no, de_s.salary as emp_salary, dm_s.salary as manager_salary from
    (select de.emp_no,s.salary,de.dept_no from dept_emp de, salaries s where de.emp_no=s.emp_no and  s.to_date='9999-01-01') as de_s 
    inner join 
    (select dm.emp_no,s.salary,dm.dept_no from dept_manager dm, salaries s where dm.emp_no=s.emp_no and  s.to_date='9999-01-01') as dm_s on de_s.dept_no = dm_s.dept_no and de_s.salary > dm_s.salary
    
#### inner join 直接3表连接
    汇总各个部门当前员工的title类型的分配数目，结果给出部门编号dept_no、dept_name、其当前员工所有的title以及该类型title对应的数目count
    
    SELECT de.dept_no, dp.dept_name, t.title, COUNT(t.title) AS count
    FROM titles AS t INNER JOIN dept_emp AS de 
    ON t.emp_no = de.emp_no AND de.to_date = '9999-01-01' AND t.to_date = '9999-01-01'
    INNER JOIN departments AS dp 
    ON de.dept_no = dp.dept_no
    GROUP BY de.dept_no, t.title
    
#### 给出每个员工每年薪水涨幅超过5000的员工编号emp_no、薪水变更开始日期from_date以及薪水涨幅值salary_growth，并按照salary_growth逆序排列。

    提示：在sqlite中获取datetime时间对应的年份函数为strftime('%Y', to_date)
    SELECT s2.emp_no, s2.from_date, (s2.salary - s1.salary) AS salary_growth
    FROM salaries AS s1, salaries AS s2
    WHERE s1.emp_no = s2.emp_no 
    AND salary_growth > 5000
    AND (strftime("%Y",s2.to_date) - strftime("%Y",s1.to_date) = 1 
         OR strftime("%Y",s2.from_date) - strftime("%Y",s1.from_date) = 1 )
    ORDER BY salary_growth DESC

#### 子查询
    
    使用子查询的方式找出属于Action分类的所有电影对应的title,description
    select f.title,f.description from film f
    where f.film_id in (select film_id from film_category fc where fc.category_id = 
    (select category_id from category where `name`='Action'))
    
#### 获取执行计划
    explain select * from employees;
    
#### 字符串拼接
    MySQL
    select concat(`last_name`,' ', first_name) Name from employees
    SQLlite
    SELECT last_name||" "||first_name AS Name FROM employees
    
#### 创建表并给默认时间为当前
    create table actor (
    actor_id smallint(5) not null,
    first_name varchar(45) not null,
    last_name varchar(45) not null,
    last_update timestamp not null default now(),
    primary key (actor_id)
    );
    
#### 插入
    insert into actor values (1,'PENELOPE','GUINESS','2006-02-15 12:34:33');
    
    如果已存在则忽略
    
    insert IGNORE into actor values(3,'ED','CHASE','2006-02-15 12:34:33');
    
#### 创建表并从旧表插入
      create table actor_name select first_name,last_name from actor;
      
#### 创建索引和唯一索引
    CREATE INDEX indexName ON mytable(username(length)); 
    
    CREATE UNIQUE INDEX indexName ON mytable(username(length)) 
    
    删除
    DROP INDEX [indexName] ON mytable; 
    
    显示
    SHOW INDEX FROM table_name
    
#### 创建视图
    视图是一种虚拟的表,是从数据库中一个或者多个表中导出的表。数据库只存放了视图的定义,而并没有存放视图中的数据,这些数据存放在原来的表中
    
    CREATE OR REPLACE VIEW v3 AS SELECT * FROM t_book WHERE id =3;
    
    删除
    DROP VIEW IF EXISTS v4;
    
#### 强制使用索引查询 FORCE INDEX name
    SELECT * FROM salaries FORCE INDEX idx_emp_no WHERE emp_no = 10005
    
#### alter表结构命令
    ALTER TABLE testalter_tbl  DROP i;
    
    ALTER TABLE testalter_tbl ADD i INT;
    
    修改字段类型及名称
    ALTER TABLE testalter_tbl MODIFY c CHAR(10);
    
    ALTER TABLE testalter_tbl CHANGE i j BIGINT;
    
    修改字段默认值
    ALTER TABLE testalter_tbl ALTER i SET DEFAULT 1000;
    
    改表名
    alter table titles_test rename titles_2017
    
#### 构造触发器
    触发器很耗费资源，尽量少用
    create trigger audit_log after insert on employees_test
    begin
         insert into audit values(new.id,new.name);
    end;
    
#### 删除
    删除emp_no重复的记录，只保留最小的id对应的记录。
    
    delete from titles_test where id not in (select min(id) from titles_test group by emp_no)
    
#### 更新数据update
    update titles_test set to_date=null,from_date='2001-01-01' where to_date='9999-01-01';
    
#### 替换replace
    REPLACE INTO titles_test VALUES (5, 10005, 'Senior Engineer', '1986-06-26', '9999-01-01')
    
#### 外键
    alter table audit add  foreign key(emp_no) references employees_test(id)
    
#### 截取字符串
    select first_name from employees order by right(first_name,2);
    
#### 聚合函数GROUP_CONCAT
    GROUP_CONCAT，这个函数很强大，直接可以吧group by后的结果拼接成一个长字符串，默认用“，”分割
    SELECT dept_no, group_concat(emp_no) as employees from dept_emp group by dept_no;
    
#### 分页
    第一条记录序号为0
    SELECT * FROM employees LIMIT 5 OFFSET 5
    
    方法二：只利用 LIMIT 关键字。注意：在 LIMIT X,Y 中，Y代表返回几条记录，X代表从第几条记录开始返回（第一条记录序号为0），切勿记反。
    SELECT * FROM employees LIMIT 5,5
    
#### exists
    EXISTS用于检查子查询是否至少会返回一行数据，该子查询实际上并不返回任何数据，而是返回值True或False。
    SELECT * FROM employees WHERE NOT EXISTS (SELECT emp_no FROM dept_emp WHERE emp_no = employees.emp_no)
    
#### case条件表达式
    SELECT e.emp_no, e.first_name, e.last_name, b.btype, s.salary, 
    (CASE b.btype 
     WHEN 1 THEN s.salary * 0.1
     WHEN 2 THEN s.salary * 0.2
     ELSE s.salary * 0.3 END) AS bonus
    FROM employees AS e INNER JOIN emp_bonus AS b ON e.emp_no = b.emp_no
    INNER JOIN salaries AS s ON e.emp_no = s.emp_no AND s.to_date = '9999-01-01'