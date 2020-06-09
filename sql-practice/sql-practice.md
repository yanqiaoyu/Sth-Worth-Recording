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
where hire_date = 
(select hire_date from employees order by hire_date desc limit 2,1);
```

## 3.查找各个部门当前\(dept\_manager.to\_date='9999-01-01'\)领导当前\(salaries.to\_date='9999-01-01'\)薪水详情以及其对应部门编号dept\_no \(注:请以salaries表为主表进行查询，输出结果以salaries.emp\_no升序排序，并且请注意输出结果，dept\_no列是最后一列\)

```sql
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL, --  '员工编号',
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
CREATE TABLE `dept_manager` (
`dept_no` char(4) NOT NULL, -- '部门编号'
`emp_no` int(11) NOT NULL, --  '员工编号'
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));
```

 思路:



```sql
select s.*, d.dept_no from salaries as s, dept_manager as d
where s.to_date='9999-01-01' and d.to_date='9999-01-01'
and s.emp_no=d.emp_no;
```

## 4.查找所有已经分配部门的员工的last\_name和first\_name以及dept\_no\(请注意输出描述里各个列的前后顺序\)

```sql
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```

思路:需要用到多个表格的时候，如果需要合并数据，需要给他们起别名

```sql
select e.last_name, e.first_name, d.dept_no 
from employees as e, dept_emp as d
where e.emp_no = d.emp_no;
```

## 5.查找所有员工的last\_name和first\_name以及对应部门编号dept\_no，也包括暂时没有分配具体部门的员工\(请注意输出描述里各个列的前后顺序\)

```sql
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```

思路：这题与上一题的唯一区别在于，需要显示左表格中一些不在右表格的数据，因此这就需要用到左联右联了

```sql
select e.last_name, e.first_name, d.dept_no
from employees e
left join dept_emp d
on e.emp_no = d.emp_no;
```

