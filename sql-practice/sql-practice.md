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
  
此题应注意以下四点： 

1、用COUNT\(\)函数和GROUP BY语句可以统计同一emp\_no值的记录条数

 2、根据题意，输出的涨幅次数为t，故用AS语句将COUNT\(emp\_no\)的值转换为t 

3、由于COUNT\(\)函数不可用于WHERE语句中，故使用HAVING语句来限定t&gt;15的条件 

4、最后存在一个理解误区，涨幅超过15次，salaries中相应的记录数应该超过16（从第2条记录开始算作第1次涨幅），不过题目为了简单起见，将第1条记录当作第1次涨幅，所以令t&gt;15即可 _/\*\*  注意：_ _严格来说，下一条salary高于本条才算涨幅，但本题只要出现了一条记录就算一次涨幅，salary相同可以理解为涨幅为0，salary变少理解为涨幅为负 \*\*/_

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

## Important 11.获取所有员工当前的\(dept\_manager.to\_date='9999-01-01'\)manager，如果员工是manager的话不显示\(也就是如果当前的manager是自己的话结果不显示\)。输出结果第一列给出当前员工的emp\_no,第二列给出其manager对应的emp\_no。

```sql
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL, -- '所有的员工编号'
`dept_no` char(4) NOT NULL, -- '部门编号'
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));
CREATE TABLE `dept_manager` (
`dept_no` char(4) NOT NULL, -- '部门编号'
`emp_no` int(11) NOT NULL, -- '经理编号'
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));

```

思路：这里最大的问题在于，我们要找的共性是什么？用什么来连接这两张表？

```sql
select de.emp_no, dm.emp_no as manager_no
from dept_emp as de, dept_manager as dm
where de.to_date = '9999-01-01'
and dm.to_date = '9999-01-01'
and de.emp_no not in (select emp_no from dept_manager)
and de.dept_no = dm.dept_no;
```

## Notice 12.获取所有部门中当前\(dept\_emp.to\_date = '9999-01-01'\)员工当前\(salaries.to\_date='9999-01-01'\)薪水最高的相关信息，给出dept\_no, emp\_no以及其对应的salary

```sql
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
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

思路:这题其实有问题，使用group by子句时，select子句中只能有聚合键、聚合函数、常数。 emp\_no并不符合这个要求。

```sql
select de.dept_no, de.emp_no, s.salary
from dept_emp de inner join salaries s
on de.emp_no = s.emp_no
and de.to_date = '9999-01-01'
and s.to_date = '9999-01-01'
where s.salary = 
    (
        select max(s2.salary)
        from dept_emp de2 inner join salaries s2
        on de2.emp_no = s2.emp_no
        and de2.to_date = '9999-01-01'
        and s2.to_date = '9999-01-01'
        where de2.dept_no = de.dept_no
        group by de2.dept_no
    )
order by de.dept_no;
```

## Important 13.从titles表获取按照title进行分组，每组个数大于等于2，给出title以及对应的数目t。

```sql
CREATE TABLE IF NOT EXISTS "titles" (
`emp_no` int(11) NOT NULL,
`title` varchar(50) NOT NULL,
`from_date` date NOT NULL,
`to_date` date DEFAULT NULL);
```

思路:看到了分组，自然而然的要想到group by  
  
1、用COUNT\(\)函数和GROUP BY语句可以统计同一title值的记录条数

 2、根据题意，输出每个title的个数为t，故用AS语句将COUNT\(title\)的值转换为t 

3、由于WHERE后不可跟COUNT\(\)函数，故用HAVING语句来限定t&gt;=2的条件

```sql
select title, count(title) as t 
from titles
group by title having t >= 2;
```

## Done 14.从titles表获取按照title进行分组，每组个数大于等于2，给出title以及对应的数目t。 注意对于重复的emp\_no进行忽略\(即emp\_no重复的title不计算，title对应的数目t不增加\)。

```sql
CREATE TABLE IF NOT EXISTS `titles` (
`emp_no` int(11) NOT NULL,
`title` varchar(50) NOT NULL,
`from_date` date NOT NULL,
`to_date` date DEFAULT NULL);
```

思路：与前面一题类似，但是需要去重，下面给出了两种解决办法

```sql
select title, count(title) as t 
from 
    (
        select distinct emp_no, * 
        from titles
    )
group by title having t >= 2;

SELECT title, COUNT(DISTINCT emp_no) AS t FROM titles
GROUP BY title HAVING t >= 2
```

## Notice 15.查找employees表所有emp\_no为奇数，且last\_name不为Mary\(注意大小写\)的员工信息，并按照hire\_date逆序排列\(题目不能使用mod函数\)

```sql
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```

思路：这题还是挺有意思的

主要问题：奇数怎么处理

```sql
select emp_no, birth_date, first_name, last_name, gender, hire_date
from employees
where emp_no % 2 = 1
and last_name != 'Mary'
order by hire_date desc;
```

## Done 16.统计出当前\(titles.to\_date='9999-01-01'\)各个title类型对应的员工当前\(salaries.to\_date='9999-01-01'\)薪水对应的平均工资。结果给出title以及平均工资avg。

```sql
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
CREATE TABLE IF NOT EXISTS "titles" (
`emp_no` int(11) NOT NULL,
`title` varchar(50) NOT NULL,
`from_date` date NOT NULL,
`to_date` date DEFAULT NULL);
```

思路：按部就班即可

```sql
select t.title, avg(s.salary) as avg
from 
salaries as s 
inner join 
titles as t 
on s.emp_no = t.emp_no
where t.to_date = '9999-01-01'
and s.to_date = '9999-01-01'
group by t.title;
```

## Notice 17.获取当前（to\_date='9999-01-01'）薪水第二多的员工的emp\_no以及其对应的薪水salary

```sql
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```

思路：这里注意 distinct 与 limit的用法即可

```sql
select emp_no, salary
from salaries
where to_date = '9999-01-01'
and salary = 
    (
        select distinct salary 
        from salaries
        order by salary desc limit 1,1
    );
```

## Important 18.查找当前薪水\(to\_date='9999-01-01'\)排名第二多的员工编号emp\_no、薪水salary、last\_name以及first\_name，你可以不使用order by完成吗

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

思路：

1.不使用order by，那么就排除Max

2.第二种方法参考了牛客网的答案，两个相同表的自连接，可以求任意高的工资

```sql
select e.emp_no, s.salary, e.last_name, e.first_name
from employees as e 
inner join 
salaries as s 
on e.emp_no = s.emp_no
where s.salary !=(
                    select max(salary)
                    from salaries
                  )
order by s.salary desc limit 0,1;


第二种 通用型可以求任意第几高，并且可以求多个形同工资
select e.emp_no,s.salary,e.last_name,e.first_name
from
employees e
join 
salaries s on e.emp_no=s.emp_no 
and  s.to_date='9999-01-01'
and s.salary = 
(
     select s1.salary
     from 
     salaries s1
     join
     salaries s2 on s1.salary<=s2.salary 
     and s1.to_date='9999-01-01' and s2.to_date='9999-01-01'
     group by s1.salary
     having count(distinct s2.salary)=2
 )
```

## Notice 19.查找所有员工的last\_name和first\_name以及对应的dept\_name，也包括暂时没有分配部门的员工

```sql
CREATE TABLE `departments` (
`dept_no` char(4) NOT NULL,
`dept_name` varchar(40) NOT NULL,
PRIMARY KEY (`dept_no`));
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

思路：本题主要需要弄清楚的地方在于3个表的联合查询，并且第一张表与第三张表没有可以联合的字段的时候如何查询

```sql
select e.last_name, e.first_name, dp.dept_name
from employees as e 
left join 
    (
        dept_emp as de 
        inner join
        departments as dp1
        on de.dept_no = dp1.dept_no
    ) as dp
on e.emp_no = dp.emp_no;
```

## Done 20.查找员工编号emp\_no为10001其自入职以来的薪水salary涨幅\(总共涨了多少\)growth\(可能有多次涨薪，没有降薪\)

```sql
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```

思路：

1.第一种最高减最低不太严谨

2.第二种按照了时间来找，防止出现中间存在降薪的问题

```sql
select (max(salary)-min(salary)) as growth
from salaries
where emp_no = 10001;


SELECT 
    ( 
        (SELECT salary FROM salaries 
        WHERE emp_no = 10001 
        ORDER BY to_date DESC LIMIT 1) 
        -
        (SELECT salary FROM salaries 
        WHERE emp_no = 10001 
        ORDER BY to_date ASC LIMIT 1)
    ) 
AS growth
```

## Important 21.查找所有员工自入职以来的薪水涨幅情况，给出员工编号emp\_no以及其对应的薪水涨幅growth，并按照growth进行升序 （注:可能有employees表和salaries表里存在记录的员工，有对应的员工编号和涨薪记录，但是已经离职了，离职的员工salaries表的最新的to\_date!='9999-01-01'，这样的数据不显示在查找结果里面）

```sql
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL, --  '入职时间'
PRIMARY KEY (`emp_no`));
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL, --  '一条薪水记录开始时间'
`to_date` date NOT NULL, --  '一条薪水记录结束时间'
PRIMARY KEY (`emp_no`,`from_date`));
```

思路:这题比上一题有意思一点，面向的是所有员工，这种时候应该怎么办呢？  
  
本题思路是先分别用两次LEFT JOIN左连接employees与salaries，建立两张表，分别存放员工当前工资（sCurrent）与员工入职时的工资（sStart），再用INNER JOIN连接sCurrent与sStart，最后限定在同一员工下用当前工资减去入职工资。

```sql
select CurrentSalary.emp_no, (CurrentSalary.salary - FirstSalary.salary) as growth
from 
    (
        select s.salary, e.emp_no
        from
        employees as e 
        inner join
        salaries as s 
        on s.emp_no = e.emp_no
        where s.to_date = '9999-01-01'
    ) as CurrentSalary
inner join 
    (
        select s.salary, e.emp_no
        from
        employees as e 
        inner join
        salaries as s 
        on s.emp_no = e.emp_no
        where s.from_date = e.hire_date
    ) as FirstSalary
on CurrentSalary.emp_no = FirstSalary.emp_no
order by growth;
```

## Done 22.统计各个部门的工资记录数，给出部门编码dept\_no、部门名称dept\_name以及部门在salaries表里面有多少条记录sum

```sql
CREATE TABLE `departments` (
`dept_no` char(4) NOT NULL,
`dept_name` varchar(40) NOT NULL,
PRIMARY KEY (`dept_no`));
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
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

思路：3表联合查询，仍然是注意联合的方法

```sql
select d.dept_no, d.dept_name, count(s.salary) as sum
from 
    (
        departments as dep 
        inner join
        dept_emp as dp 
        on dep.dept_no = dp.dept_no
    ) as d 
inner join
salaries as s 
on d.emp_no = s.emp_no
group by d.dept_no;
```

## Important 23.对所有员工的当前\(to\_date='9999-01-01'\)薪水按照salary进行按照1-N的排名，相同salary并列且按照emp\_no升序排列

```sql
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```

思路:参考了牛客网的思路  
本题的主要思想是**复用salaries表进行比较排名**，具体思路如下： 

1、从两张相同的salaries表（分别为s1与s2）进行对比分析，先将两表限定条件设为to\_date = '9999-01-01'，挑选出当前所有员工的薪水情况。

 2、本题的精髓在于 **s1.salary &lt;= s2.salary，意思是在输出s1.salary的情况下，有多少个s2.salary大于等于s1.salary**，比如当s1.salary=94409时，有3个s2.salary（分别为94692,94409,94409）大于等于它，但由于94409重复，利用COUNT\(DISTINCT s2.salary\)去重可得工资为94409的rank等于2。其余排名以此类推。 

3、千万不要忘了GROUP BY s1.emp\_no，否则输出的记录只有一条（可能是第一条或者最后一条，根据不同的数据库而定），因为用了合计函数COUNT\(\)

4、最后先以 s1.salary 逆序排列，再以 s1.emp\_no 顺序排列输出结果

```sql
select s1.emp_no, s1.salary, count(distinct s2.salary) as rank
from 
salaries as s1,
salaries as s2
where s1.to_date = '9999-01-01' 
and s2.to_date = '9999-01-01'
and s1.salary <= s2.salary
group by s1.emp_no
order by  s1.salary desc,s1.emp_no asc;
```

## Done 24.获取所有非manager员工当前的薪水情况，给出dept\_no、emp\_no以及salary ，当前表示to\_date='9999-01-01'

```sql
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));
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
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```

思路:按部就班即可

```sql
select de.dept_no, de.emp_no, s.salary
from 
(
    dept_emp as de 
    inner join 
    salaries as s 
    on de.emp_no = s.emp_no
)
where s.to_date = '9999-01-01'
and de.emp_no not in 
(select emp_no from dept_manager 
where to_date = '9999-01-01');
```

## Important 25.获取员工其当前的薪水比其manager当前薪水还高的相关信息，当前表示to\_date='9999-01-01', 结果第一列给出员工的emp\_no， 第二列给出其manager的manager\_no， 第三列给出该员工当前的薪水emp\_salary, 第四列给该员工对应的manager当前的薪水manager\_salary

```sql
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));
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

思路：其实跟前几题的自连接查询略像

```sql
select se.emp_no, 
sm.emp_no as manager_no, 
se.salary as emp_salary, 
sm.salary as manager_salary
from 
(
    dept_emp as de 
    inner join 
    salaries as s1
    on de.emp_no = s1.emp_no
    and s1.to_date = '9999-01-01'
) as se,
(
    dept_manager as dm
    inner join 
    salaries as s2
    on dm.emp_no = s2.emp_no
    and s2.to_date = '9999-01-01'
) as sm
where sm.dept_no = se.dept_no
and sm.salary < se.salary;

```

## Important 26.汇总各个部门当前员工的title类型的分配数目，即结果给出部门编号dept\_no、dept\_name、其部门下所有的当前\(dept\_emp.to\_date = '9999-01-01'\)员工的当前\(titles.to\_date = '9999-01-01'\)title以及该类型title对应的数目count \(注：因为员工可能有离职，所有dept\_emp里面to\_date不为'9999-01-01'就已经离职了，不计入统计，而且员工可能有晋升，所以如果titles.to\_date 不为 '9999-01-01'，那么这个可能是员工之前的职位信息，也不计入统计\)

```sql
CREATE TABLE `departments` (
`dept_no` char(4) NOT NULL,
`dept_name` varchar(40) NOT NULL,
PRIMARY KEY (`dept_no`));
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));
CREATE TABLE IF NOT EXISTS `titles` (
`emp_no` int(11) NOT NULL,
`title` varchar(50) NOT NULL,
`from_date` date NOT NULL,
`to_date` date DEFAULT NULL);
```

思路：最为关键的一步在于，需要对dept\_no也分组

```sql
select dep.dept_no, dep.dept_name, t_and_de.title, count(t_and_de.title) as count
from 
(
    titles as t 
    inner join
    dept_emp as de 
    on t.emp_no = de.emp_no
    and t.to_date = '9999-01-01'
    and de.to_date = '9999-01-01'
) as t_and_de
inner join departments as dep
on de.dept_no = dep.dept_no
group by de.dept_no, t.title;
```

## Note 27.给出每个员工每年薪水涨幅超过5000的员工编号emp\_no、薪水变更开始日期from\_date以及薪水涨幅值salary\_growth，并按照salary\_growth逆序排列。 提示：在sqlite中获取datetime时间对应的年份函数为strftime\('%Y', to\_date\) \(数据保证每个员工的每条薪水记录to\_date-from\_date=1年，而且同一员工的下一条薪水记录from\_data=上一条薪水记录的to\_data\)

```sql
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
如：插入
INSERT INTO salaries VALUES(10001,52117,'1986-06-26','1987-06-26');
INSERT INTO salaries VALUES(10001,62102,'1987-06-26','1988-06-25');
INSERT INTO salaries VALUES(10002,72527,'1996-08-03','1997-08-03');
INSERT INTO salaries VALUES(10002,72527,'1997-08-03','1998-08-03');
INSERT INTO salaries VALUES(10002,72527,'1998-08-03','1999-08-03');
INSERT INTO salaries VALUES(10003,43616,'1996-12-02','1997-12-02');
INSERT INTO salaries VALUES(10003,43466,'1997-12-02','1998-12-02');
```

思路:其实看到求薪水的涨幅，有了前面的经验，可以很轻易的想到 引用两次salaries的表，自连接进行涨幅筛选，难点在于怎么体现这个 一年 ，题目里面又给了提示，由此可解

```sql
select s2.emp_no, s2.from_date, (s2.salary - s1.salary) as salary_growth
from salaries as s1
inner join
salaries as s2
on s1.emp_no = s2.emp_no
and s2.salary - s1.salary > 5000
and strftime('%Y',s2.to_date)-strftime('%Y',s1.to_date)=1
order by salary_growth desc;
```

## Note 28.查找描述信息\(film.description\)中包含robot的电影对应的分类名称\(category.name\)以及电影数目\(count\(film.film\_id\)\)，而且还需要该分类包含电影总数量\(count\(film\_category.category\_id\)\)&gt;=5部

思路：这题有点睿智了，就当做是考察 LIKE这个关键字了

```sql
我们知道在 MySQL 中使用 SQL SELECT 命令来读取数据， 同时我们可以在 SELECT 语句中使用 WHERE 子句来获取指定的记录。

WHERE 子句中可以使用等号 = 来设定获取数据的条件，如 "runoob_author = 'RUNOOB.COM'"。

但是有时候我们需要获取 runoob_author 字段含有 "COM" 字符的所有记录，这时我们就需要在 WHERE 子句中使用 SQL LIKE 子句。

SQL LIKE 子句中使用百分号 %字符来表示任意字符，类似于UNIX或正则表达式中的星号 *。

SELECT field1, field2,...fieldN 
FROM table_name
WHERE field1 LIKE condition1 [AND [OR]] filed2 = 'somevalue'
```

```sql
SELECT c.name AS name, COUNT(f.film_id) AS amount
FROM film AS f, film_category AS fc, category AS c,
(SELECT category_id FROM film_category GROUP BY category_id HAVING COUNT(category_id) >= 5) AS cc
WHERE f.description LIKE '%robot%'
AND f.film_id = fc.film_id
AND fc.category_id = c.category_id
AND c.category_id = cc.category_id
```

## Done 29.使用join查询方式找出没有分类的电影id以及名称

```sql
CREATE TABLE IF NOT EXISTS film (
film_id smallint(5)  NOT NULL DEFAULT '0',
title varchar(255) NOT NULL,
description text,
PRIMARY KEY (film_id));

CREATE TABLE category  (
category_id  tinyint(3)  NOT NULL ,
name  varchar(25) NOT NULL, `last_update` timestamp,
PRIMARY KEY ( category_id ));

CREATE TABLE film_category  (
film_id  smallint(5)  NOT NULL,
category_id  tinyint(3)  NOT NULL, `last_update` timestamp);
```

解题思路是**运用 LEFT JOIN 连接两表，用 IS NULL 语句限定条件**：

 1、用 LEFT JOIN 连接 film 和 film\_category，限定条件为 f.film\_id = fc.film\_id，即连接电影 id 和电影分类 id，如果电影没有分类，则电影分类 id 显示 null 2、再用 WHERE 来限定条件 fc.category\_id IS NULL 选出没分类的电影  


 /\* 注意：最后一句若写成 ON f.film\_id = fc.film\_id **AND** fc.category\_id IS NULL，则意义变成左连接两表 film\_id 相同的记录，且 film\_category 原表中的 fc.category 的值为 null。显然，原表中的 fc.category 的值恒不为 null，因此（f.film\_id = fc.film\_id **AND** fc.category\_id IS NULL）恒为 FALSE，左连接后则只会显示 film 表的数据，而 film\_category 表的数据全显示为 null \*/

```sql
SELECT f.film_id, f.title 
FROM film f 
LEFT JOIN 
film_category fc
ON f.film_id = fc.film_id 
WHERE fc.category_id IS NULL
```

## Done 30.你能使用子查询的方式找出属于Action分类的所有电影对应的title,description吗

```sql
CREATE TABLE IF NOT EXISTS film (
film_id smallint(5)  NOT NULL DEFAULT '0',
title varchar(255) NOT NULL,
description text,
PRIMARY KEY (film_id));

CREATE TABLE category  (
category_id  tinyint(3)  NOT NULL ,
name  varchar(25) NOT NULL, `last_update` timestamp,
PRIMARY KEY ( category_id ));

CREATE TABLE film_category  (
film_id  smallint(5)  NOT NULL,
category_id  tinyint(3)  NOT NULL, `last_update` timestamp);
```

思路：

1.什么是子查询

在一个表表达中可以调用另一个表表达式，这个被调用的表表达式叫做子查询（subquery），我么也称作子选择（subselect）或内嵌选择（inner select）。子查询的结果传递给调用它的表表达式继续处理。

说白了就是嵌套语句

```sql
select f.title, f.description
from film as f 
where f.film_id in 
(
    select fc.film_id from
    film_category as fc 
    inner join 
    category as c 
    on fc.category_id = c.category_id
    and c.name = 'Action'
    
)
```

## Note 31.获取select \* from employees对应的执行计划

思路：

explain 这个关键字就是用来解释执行计划的

```sql
explain select * from employees
```

## Note 32.将employees表的所有员工的last\_name和first\_name拼接起来作为Name，中间以一个空格区分

```sql
CREATE TABLE `employees` ( `emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```

思路：注意在sqlite和mysql，拼接的办法里面不一致

```sql
select (employees.last_name || ' ' || employees.first_name) as Name
from employees;

select CONCAT(last_name," "，first_name) as name  from employees
```

## IMPORTANT 33.创建一个actor表，包含如下列信息\(注：sqlite获取系统默认时间是datetime\('now','localtime'\)\)

```sql

列表	类型	是否为NULL	含义
actor_id	smallint(5)	not null	主键id
first_name	varchar(45)	not null	名字
last_name	varchar(45)	not null	姓氏
last_update	timestamp	not null	最后更新时间，默认是系统的当前时间
```

思路：创建新表的基本语法，注意主键如何定义

```sql
create table actor
(
    actor_id smallint(5) not null,
    first_name varchar(45) 	not null,
    last_name varchar(45) 	not null,
    last_update timestamp not null default(datetime('now','localtime')),
    primary key(actor_id)
);
```

## IMPORTANT 34.对于表actor批量插入如下数据\(不能有2条insert语句哦!\)

```sql
CREATE TABLE IF NOT EXISTS actor (
actor_id smallint(5) NOT NULL PRIMARY KEY,
first_name varchar(45) NOT NULL,
last_name varchar(45) NOT NULL,
last_update timestamp NOT NULL DEFAULT (datetime('now','localtime')))

actor_id	first_name	last_name	last_update
1	PENELOPE	GUINESS	2006-02-15 12:34:33
2	NICK	WAHLBERG	2006-02-15 12:34:33
```

 思路:考察批量插入

```sql
以下为向MySQL数据表插入数据通用的 INSERT INTO SQL语法：

INSERT INTO table_name ( field1, field2,...fieldN )
                       VALUES
                       ( value1, value2,...valueN );
```

```sql
insert into actor 
values(1, "PENELOPE", "GUINESS", "2006-02-15 12:34:33"),
      (2, "NICK", "WAHLBERG", "2006-02-15 12:34:33")
```

## IMPORTANT 35.对于表actor批量插入如下数据,如果数据已经存在，请忽略\(不支持使用replace操作\)

```sql
CREATE TABLE IF NOT EXISTS actor (
actor_id smallint(5) NOT NULL PRIMARY KEY,
first_name varchar(45) NOT NULL,
last_name varchar(45) NOT NULL,
last_update timestamp NOT NULL DEFAULT (datetime('now','localtime')))


actor_id	first_name	last_name	last_update
'3'	'ED'	'CHASE'	'2006-02-15 12:34:33'
```

思路：语法，没有思路，注意，这个语句在mysql与sqlite中也不一样

```sql
insert or ignore into actor
values('3', 'ED', 'CHASE', '2006-02-15 12:34:33');

insert IGNORE into actor
values(3,'ED','CHASE','2006-02-15 12:34:33');
```

