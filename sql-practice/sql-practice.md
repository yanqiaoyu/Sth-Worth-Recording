# SQL-Practice

## 1.查找最晚入职员工的所有信息，为了减轻入门难度，目前所有的数据里员工入职的日期都不是同一天\(sqlite里面的注释为--,mysql为comment\) 

```sql
CREATE TABLE employees
 ( emp_no int(11) NOT NULL, -- '员工编号' 
birth_date date NOT NULL, 
first_name varchar(14) NOT NULL, 
last_name varchar(16) NOT NULL, 
gender char(1) NOT NULL, 
hire_date date NOT NULL, 
PRIMARY KEY (emp_no));
```

思路：

1. 要找的是XX的信息，那就先找出所有XX来
2. XX的所有信息，所以要显示所有信息
3. 要找的是最晚入职时间，那么就应该围绕 hire\_date过滤

```sql

select * from employees
where hire_date = 
(select max(hire_date) from employees);



select * from employees
where hire_date = 
(select hire_date from employees order by hire_date desc limit 1)

```

## 2.查找入职员工时间排名倒数第三的员工所有信息，为了减轻入门难度，目前所有的数据里员工入职的日期都不是同一天 

```sql
CREATE TABLE employees 
( emp_no int(11) NOT NULL, 
birth_date date NOT NULL, 
first_name varchar(14) NOT NULL, 
last_name varchar(16) NOT NULL, 
gender char(1) NOT NULL, 
hire_date date NOT NULL, 
PRIMARY KEY (emp_no));
```

思路:

1. 要找的是员工，那肯定先select出所有员工
2. 根据入职时间来过滤 考虑 where hire\_date = 
3. 那后面的过滤条件就应该是找“倒数第三“了
4. 要找出所有入职时间里面的倒数第三，肯定先要找出所有入职时间，那就是 select hire\_date from employees 
5. 然后排序，肯定是 order by hire\_date 
6. 找的是倒数, 那还要加个 desc 
7. 然后是倒数第三，那么用limit i, n（其中i指的是偏移量，默认0，n指的是返回多少查询结果）

```sql
select * from employees
```

