# SQL-Practice

## Done 1.查找最晚入职员工的所有信息，为了减轻入门难度，目前所有的数据里员工入职的日期都不是同一天\(sqlite里面的注释为--,mysql为comment\) 

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

## Done 2.查找入职员工时间排名倒数第三的员工所有信息，为了减轻入门难度，目前所有的数据里员工入职的日期都不是同一天 

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

思路:主要考验去重

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

## Done 3.查找各个部门当前\(dept\_manager.to\_date='9999-01-01'\)领导当前\(salaries.to\_date='9999-01-01'\)薪水详情以及其对应部门编号dept\_no \(注:请以salaries表为主表进行查询，输出结果以salaries.emp\_no升序排序，并且请注意输出结果，dept\_no列是最后一列\)

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

 思路:联合查询，限定时间

```sql
select s.*, d.dept_no from salaries as s, dept_manager as d
where s.to_date='9999-01-01' and d.to_date='9999-01-01'
and s.emp_no=d.emp_no;
```

## Done 4.查找所有已经分配部门的员工的last\_name和first\_name以及dept\_no\(请注意输出描述里各个列的前后顺序\)

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

## Done 5.查找所有员工的last\_name和first\_name以及对应部门编号dept\_no，也包括暂时没有分配具体部门的员工\(请注意输出描述里各个列的前后顺序\)

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

思路：这题与上一题的唯一区别在于，需要显示左表格中一些不在右表格的数据，因此这就需要用到左联

```sql
select e.last_name, e.first_name, d.dept_no
from employees e
left join dept_emp d
on e.emp_no = d.emp_no;
```

## Done 6.查找所有员工入职时候的薪水情况，给出emp\_no以及salary， 并按照emp\_no进行逆序\(请注意，一个员工可能有多次涨薪的情况\)

```sql
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```

思路：自己写的时候忘记了一个限制条件

```sql
e.emp_no = s.emp_no
```

这里要加上这个条件的原因在于：  
  
由于测试数据中，salaries.emp\_no 不唯一（因为号码为 emp\_no 的员\*\*\*有多次涨薪的可能，所以在 salaries 中对应的记录不止一条），employees.emp\_no 唯一，即 salaries 的数据会多于 employees，因此需先找到 employees.emp\_no 在 salaries 表中对应的记录salaries.emp\_no，则有限制条件 e.emp\_no = s.emp\_no

```sql
select e.emp_no, s.salary
from employees as e, salaries as s
where e.hire_date = s.from_date and e.emp_no = s.emp_no
order by e.emp_no desc;
```

## Important 7.查找薪水变动超过15次的员工号emp\_no以及其对应的变动次数t

```sql
CREATE TABLE `salaries` (
 `emp_no` int(11) NOT NULL,
 `salary` int(11) NOT NULL,
 `from_date` date NOT NULL,
 `to_date` date NOT NULL,
 PRIMARY KEY (`emp_no`,`from_date`));
```

思路：这里参考了别人的答案  
  
此题应注意以下四点： 1、用COUNT\(\)函数和GROUP BY语句可以统计同一emp\_no值的记录条数 2、根据题意，输出的涨幅次数为t，故用AS语句将COUNT\(emp\_no\)的值转换为t 3、由于COUNT\(\)函数不可用于WHERE语句中，故使用HAVING语句来限定t&gt;15的条件 4、最后存在一个理解误区，涨幅超过15次，salaries中相应的记录数应该超过16（从第2条记录开始算作第1次涨幅），不过题目为了简单起见，将第1条记录当作第1次涨幅，所以令t&gt;15即可 _/\*\*  注意：_ _严格来说，下一条salary高于本条才算涨幅，但本题只要出现了一条记录就算一次涨幅，salary相同可以理解为涨幅为0，salary变少理解为涨幅为负 \*\*/_

```sql
SELECT emp_no, COUNT(emp_no) AS t FROM salaries
group by emp_no having t > 15;
```

## Note 8.找出所有员工当前\(to\_date='9999-01-01'\)具体的薪水salary情况，对于相同的薪水只显示一次,并按照逆序显示

```sql
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```

思路：没啥好说的

```sql
select distinct salary from salaries
where to_date = '9999-01-01'
order by salary desc;

select salary from salaries
where to_date = '9999-01-01'
group by salary
order by salary desc;
```

注意：

对于distinct与group by的使用: 

1、当对系统的性能高并数据量大时使用group by 

2、当对系统的性能不高时使用数据量少时两者皆可

 3、尽量使用group by

## Done 9.获取所有部门当前\(dept\_manager.to\_date='9999-01-01'\)manager的当前\(salaries.to\_date='9999-01-01'\)薪水情况，给出dept\_no, emp\_no以及salary\(请注意，同一个人可能有多条薪水情况记录\)

```sql
CREATE TABLE `dept_manager` (
`dept_no` char(4) NOT NULL,
`emp_no` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```

思路：无需多言

```sql
select d.dept_no, d.emp_no, s.salary
from salaries as s inner join dept_manager as d 
on d.emp_no = s.emp_no
and d.to_date = '9999-01-01'
and s.to_date = '9999-01-01';
```

## Done 10.获取所有非manager的员工emp\_no

```sql
CREATE TABLE `dept_manager` (
`dept_no` char(4) NOT NULL,
`emp_no` int(11) NOT NULL,
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

 思路: 采用 not in 即可

```sql
select emp_no 
from employees
where emp_no not in (select emp_no from dept_manager);
```



