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
    
    select e.emp_no from employees e left join dept_manager d on e.emp_no == d.emp_no where d.emp_no is Null;
    
#### 获取所有员工当前的manager，如果当前的manager是自己的话结果不显示，当前表示to_date='9999-01-01'。
    select e.emp_no, m.emp_no as manager_no from dept_emp e 
    inner join dept_manager m on e.dept_no == m.dept_no
    where m.to_date='9999-01-01' and e.to_date='9999-01-01' and m.emp_no <> e.emp_no
    
#### distinct

MySQL发现第1个匹配行后，停止为当前的行组合搜索更多的行。

    从titles表获取按照title进行分组，每组个数大于等于2，给出title以及对应的数目t。注意对于重复的emp_no进行忽略。
    
    select title, count(distinct emp_no) t from titles group by title having title >2;
    
#### 查找employees表所有emp_no为奇数，且last_name不为Mary的员工信息，并按照hire_date逆序排列
    
    select * from employees where emp_no %2 == 1 and last_name != 'Mary' order by hire_date desc;
    
#### 查找当前薪水(to_date='9999-01-01')排名第二多的员工编号emp_no、薪水salary、last_name以及first_name，不准使用order by

    select e.emp_no, max(s.salary), e.last_name, e.first_name 
    from employees e , salaries s 
    where e.emp_no = s.emp_no and s.to_date='9999-01-01' and
    s.salary < (select max(salary) from salaries)
    
#### 三表左连接
    查找所有员工的last_name和first_name以及对应的dept_name，也包括暂时没有分配部门的员工
    
    select e.last_name, e.first_name, d.dept_name from employees e left join dept_emp de on e.emp_no = de.emp_no left join departments d on de.dept_no = d.dept_no