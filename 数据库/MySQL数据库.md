# 第一部分 MySQL基础

## 1 数据库的相关概念

特点：

- 数据持久化
- 使用完整的管理系统统一管理，易于查询

关系型数据库：存放的是实体和实体之间的关系

数据库的重要概念：

**DB**：数据库（Database），保存了一系列有组织的数据。

**DBMS**：数据库管理系统（Database Management System），数据库是DBMS创建和操作的容器

**SQL**：结构化查询语言（Structure Query Language）：专门用来与数据库通信的语言。几乎所有DBMS都支持SQL

表的概念
表名具有唯一性
表的列又叫字段

DBMS分为两类：

1. 基于共享文件系统的DBMS（如Access）
2. 基于客户机-服务器的DBMS（如MySQL、Oracle、SqlServer）

MySQL的默认端口号是3306

## 2 初始MySQL

### 启动和停止方法

方法一：计算机管理--->服务和应用程序--->服务--->MySQL

方法二：cmd用管理员身份打开，输入`net start mysqlxxx`启动；输入`net stop mysqlxxx`停止

MySQL连接数据库：

root用户可以通过mysql自带的客户端

在cmd中输入：`mysql -h localhost -P 3306 -u root -p`

连接本地主机可以简写为`mysql -u root -p`

`show databases;`

`use xxx;`

`show tables;`

`show tables from xxx;`

`select database();`

`create table xxx();`

`desc xxx;`

`select version();`

### MySQL语法规范

1. 不区分大小写，但建议关键字大写，表明、列名小写
2. 每条命令最好用分号结尾（或\g，图形化界面可以不用分号）
3. 每条命令根据需要，可以进行缩进或换行
4. 注释：单行`#`或`--`，多行`/*注释文字*/`

图形化客户端：SQLyog，Navicat





# 第二部分 SQL语言

## 1 DQL语言

### 查询

基础查询

```mysql
#进阶1：基础查询
/*
语法
select 查询列表
from 表名；

特点：
1. 查询列表可以是：表中的字段、常量值、表达式、函数
2. 查询的结果是一个虚拟的表格

*/

#1. 查询单个字段
SELECT last_name FROM employees;

#2. 查询多个字段
SELECT 
  last_name,
  salary,
  email 
FROM
  employees ;

SELECT 
  `first_name`,
  `last_name`,
  `email`,
  `phone_number`,
  `job_id` 
FROM
  employees ;

#3. 查询所有字段
SELECT * FROM employees;

#4. 查询常量值
SELECT 100;
SELECT 'john';

#5. 查询表达式
SELECT 100 % 98;

#6. 查询函数
SELECT VERSION();

#7.起别名
SELECT 100 % 98 AS result;
SELECT first_name AS 名, last_name AS 姓 FROM employees;
#使用空格代替AS也可以
SELECT first_name 名, last_name 姓 FROM employees;
SELECT first_name AS "the result" FROM employees;

#8. 去重
SELECT DISTINCT department_id FROM employees; #测试
#9. 加号的作用
/*
mysql中的加号仅作为运算符

select 10+9;
#其中一方为字符型，则试图将字符型数值转换成数值型
select '123'+90; #转换成功，显示213
select 'john'+90; #转换失败，将字符型数值转换成0
select null+10; #只要其中一方为null，则结果肯定为null

*/

#拼接字符
SELECT 
  CONCAT(first_name, ' ', last_name) AS 姓名 
FROM
  employees ;

#显示表的结构，并查询其中的全部数据
DESC departments;
SELECT * FROM `departments`;

#ifnull的用法
#是null返回指定的值，否则返回原本的值
SELECT
	CONCAT(`first_name`, ', ', `last_name`, ', ' ,`job_id`, ', ', IFNULL(commission_pct, 0)) AS output
FROM
	employees;
```

条件查询

```mysql
#进阶2：条件查询
/*
语法：
	select
		查询列表
	from
		表名
	where
		筛选条件;
执行顺序：表名--->筛选条件--->查询列表

分类：
	一、按条件表达式筛选
	
	条件运算符：> < = <=> != <> >= <=
	
	二、按逻辑表达式筛选
	
	逻辑运算符：
		&& || !
		and or not
	
	三、模糊查询
		like
		between and
		in
		is null | is not null

*/

#一、按条件表达式筛选

SELECT 
  * 
FROM
  employees 
WHERE salary > 12000 ;

SELECT 
  last_name,
  department_id 
FROM
  employees 
WHERE department_id != 90 ;

#二、按逻辑表达式筛选

SELECT 
  last_name,
  salary,
  commission_pct 
FROM
  employees 
WHERE salary >= 10000 
  AND salary <= 20000 ;

SELECT 
  * 
FROM
  employees 
WHERE NOT (
    department_id >= 90 
    AND department_id <= 110
  ) 
  OR salary > 15000 ;

#三、模糊查询
/*
like常搭配通配符
% 任意多个字符，包含0个字符
_ 任意单个字符
*/
SELECT 
  * 
FROM
  employees 
WHERE last_name LIKE '%a%' ;

SELECT 
  last_name,
  salary 
FROM
  employees 
WHERE last_name LIKE '__n_l%' ;

SELECT
last_name
FROM
employees
WHERE
last_name LIKE '__$_%' ESCAPE '$';#ESCAPE代表某字符转义

#2. between and

#注意前后两个数字的顺序
SELECT 
  * 
FROM
  employees 
WHERE employee_id BETWEEN 100 
  AND 120 ;

#3. in
#查询特定标签
SELECT 
  last_name,
  job_id 
FROM
  employees 
WHERE job_id IN ('IT_PORT', 'AD_VP', 'AD_PRES') ;

#4. is null
#是null返回1，否则返回0
SELECT 
  last_name,
  commission_pct 
FROM
  employees 
WHERE commission_pct IS NULL ;

#安全等于 <=>
#既可以判断null也可以判断普通数字

SELECT 
  last_name,
  commission_pct 
FROM
  employees 
WHERE commission_pct <=> NULL ;

SELECT 
  last_name,
  salary 
FROM
  employees 
WHERE salary <=> 10000 ;
```

排序查询

```mysql
#进阶3：排序查询

/*
语法：
	select 查询列表
	from 表
	【where 筛选条件】
	order by 排序列表 【asc | desc】
	
	1、asc代表的是升序，desc代表的是降序
	如果不写，默认升序
	2、 order by子句中可以支持单个字段、多个字段、表达式】函数】别名
	3、order by子句一般是放在查询语句的最后面，limit子句除外
*/

SELECT * FROM employees ORDER BY salary DESC;
SELECT * FROM employees ORDER BY salary;

SELECT *
FROM employees
WHERE department_id >= 90
ORDER BY hiredate ASC;

SELECT *, salary * 12 * (1 + IFNULL(commission_pct, 0) 年薪
FROM employees
orader BY salary * 12 * (1 + IFNULL(commission_pct, 0) DESC;

SELECT *, salary * 12 * (1 + IFNULL(commission_pct, 0) 年薪
FROM employees
orader BY 年薪 DESC;

SELECT LENGTH(last_name) 字节长度, last_name, salary
FROM employees
ORDER BY LENGTH(last_name) DESC;

#首先按工资升序排序，相同的工资再按降序排序
SELECT *
FROM employees
ORDER BY salary ASC, employee_id DESC;
```

常见函数之单行函数

```mysql
#进阶4：常见函数
/*
	调用：select 函数名(实参列表) 【from 表】
	分类：1. 单行函数；2. 分组函数
*/

#一、字符函数

#length
SELECT LENGTH('john');
SELECT LENGTH('李华lihua');

SHOW VARIABLES LIKE '%char%';


#2. concat 拼接字符串

SELECT CONCAT(first_name, ' ', last_name) 姓名 FROM employees;

#3. upper, lower

SELECT CONCAT(LOWER(first_name), UPPER(last_name)) 姓名 FROM employees;

#4. substr、substring
#所有SQL语言索引都从1开始
SELECT SUBSTR('李华喜欢到处旅游', 6) output;
SELECT SUBSTR('李华喜欢到处旅游', 3, 5) output;

SELECT CONCAT(UPPER(SUBSTR(last_name, 1, 1)), LOWER(SUBSTR(last_name, 2))) output
FROM employees;

#5. instr 返回子串第一次出现的索引，如果找不到返回0

SELECT INSTR('李华喜欢到处旅游', '旅游') output;

#6. trim

SELECT TRIM('    李华   ') AS output;
SELECT TRIM('a' FROM 'aaaaaaaa李华aaaaaaaaaaaaaa') AS output;

#7. lpad 用指定的字符实现左填充指定长度

SELECT LPAD('李华', 5, '*') AS output;

#8. rpad 用指定的字符实现右填充指定长度

SELECT RPAD('李华', 10, 'ab') AS output;

#9. replace 替换

SELECT REPLACE('李华喜欢到处旅游', '旅游', '观光') AS output;

#二、数学函数

#round 四舍五入
SELECT ROUND(1.65);
SELECT ROUND(1.567, 2);#保留2位小数

#ceil 向上取整
SELECT CEIL(1.32);

#floor 向下取整
SELECT FLOOR(-7.36);

#truncate 截断
SELECT TRUNCATE(1.699999, 1);#保留1位小数

#mod 取余
SELECT MOD(10, -3);
SELECT 10 % 3;

#三、日期函数

#now 返回当前系统日期+时间
SELECT NOW();

#curdate 返回当前系统日期，不包含时间
SELECT CURDATE();

#curtime 返回当前系统时间，不包含日期
SELECT CURTIME();

#可以获取指定的部分、年、月、日、小时、分钟、秒
SELECT YEAR(NOW()) 年;
SELECT YEAR('1980-1-1') 年;

SELECT YEAR(hiredate) 年 FROM employees;

SELECT MONTH(NOW()) 月;
SELECT MONTHNAME(NOW()) 月;

#str_to_date

SELECT STR_TO_DATE('1998-3-2', '%Y-%c-%d') AS output;

#date_format将日期转换成字符

SELECT DATE_FORMAT(NOW(), '%y年%m月%d日') AS output;

#四、其他函数

SELECT VERSION();
SELECT DATABASE();
SELECT USER();

#五、流程控制函数
#1. if函数

SELECT IF(5 < 10, '小', '大');

#2. case函数
/*使用一：switch case 的效果
case ...
when ... then ...
when ... then ...
...
else ...;
end
*/

SELECT salary 原始工资, department_id,
CASE department_id
WHEN 30 THEN salary * 1.1
WHEN 40 THEN salary * 1.2
WHEN 50 THEN salary * 1.3
ELSE salary
END AS 新工资
FROM employees;

/*使用二：多重if的效果

*/

SELECT salary,
CASE
WHEN salary > 20000 THEN 'A'
WHEN salary > 15000 THEN 'B'
WHEN salary > 10000 THEN 'C'
ELSE 'D'
END AS 工资级别
FROM employees;
```

常见函数之分组函数

```mysql
#二、分组函数
/*
sum 求和、avg平均值、max 最大值、min 最小值、count 计算个数

sum avg 一般用于处理数值型
max min count可以处理任何类型

以上分组函数都忽略null值

可以和distinct搭配实现去重的运算

count函数的介绍

*/

#1. 简单的使用
SELECT SUM(salary) FROM employees;
SELECT AVG(salary) FROM employees;
SELECT MIN(salary) FROM employees;
SELECT MAX(salary) FROM employees;
SELECT COUNT(salary) FROM employees;

SELECT SUM(salary) 和, ROUND(AVG(salary), 2) 平均, MAX(salary) 最高, MIN(salary) 最低, COUNT(salary) 个数
FROM employees;

#2.参数支持哪些类型

SELECT SUM(last_name), AVG(last_name) FROM employees;

SELECT MAX(last_name), MIN(last_name) FROM employees;

#3. 是否忽略null
#sum、avg、max、min、count忽略null
SELECT SUM(commission_pct), AVG(commission_pct) FROM employees;

#4. 和distinct搭配

SELECT SUM(DISTINCT salary), SUM(salary) FROM employees;

#5. count函数的详细介绍
#统计行数
SELECT COUNT(salary) FROM employees;

SELECT COUNT(*) FROM employees;

SELECT COUNT(1) FROM employees;

#6. 和分组函数一同查询的字段有限制

SELECT AVG(salary), employee_id FROM employees;
```

分组查询

```mysql
#进阶5：分组查询
/*
语法：
	select 分组函数，列（要求出现在group by的后面）
	from 表
	【where 筛选条件】
	group by 分组的列表
	【order by 子句】
注意：
	查询列表必须特殊，要求是分组函数和group by后出现的字段

特点：
	1、分组查询中的筛选条件分为两类
			数据源		位置			关键字
	分组前筛选	原始表		gruop by子句前面	where
	分组后筛选	分组后的结果集	group by子句后面	having

	2、group by子句支持单个字段分组，多个字段分组
	3、若有排序，排序放在整个分组查询的最后

*/

SELECT MAX(salary), job_id
FROM employees
GROUP BY job_id;

SELECT COUNT(*), location_id
FROM departments
GROUP BY location_id;

SELECT AVG(salary), department_id
FROM employees
WHERE email LIKE '%a%'
GROUP BY department_id;

SELECT MAX(salary), manager_id
FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY manager_id;

SELECT COUNT(*), department_id
FROM employees
GROUP BY department_id
HAVING COUNT(*)>2;

SELECT MAX(salary), job_id
FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY job_id
HAVING MAX(salary)>12000;

SELECT MIN(salary), manager_id
FROM employees
WHERE manager_id>102
GROUP BY manager_id
HAVING MIN(salary)>5000;

SELECT COUNT(*), LENGTH(last_name) len_name
FROM employees
GROUP BY LENGTH(last_name)
HAVING COUNT(*)>5;

#按多个字段分组
SELECT AVG(salary), department_id, job_id
FROM employees
WHERE department_id IS NOT NULL
GROUP BY department_id, job_id
HAVING AVG(salary)>10000
ORDER BY AVG(salary) DESC;
```

分组查询的特点：

|            | 使用关键字 | 筛选的表     | 位置           |
| ---------- | ---------- | ------------ | -------------- |
| 分组前筛选 | where      | 原始表       | group by的前面 |
| 分组后筛选 | having     | 分组后的结果 | group by的后面 |

连接查询

```mysql
#进阶6：连接查询
/*
笛卡尔乘积：当查询多个表时，没有添加有效的连接条件，导致多个表所有行实现完全连接

	sql92标准：仅仅支持内连接
	sql99标准：支持内连接+外连接（左外和右外）+交叉连接

	内连接：
		等值连接
		非等值连接
		自连接
	外连接：
		左外连接
		右外连接
		全外连接（mysql不支持）
	交叉连接

*/

SELECT * FROM beauty;
SELECT * FROM boys;

SELECT NAME, boyName
FROM boys,beauty
WHERE beauty.boyfriend_id=boys.id;

#1. 等值连接

#表的别名顺序

SELECT e.last_name, e.job_id, j.job_title
FROM jobs j, employees e
WHERE e.`job_id`=j.`job_id`;

SELECT department_name, city
FROM departments d, locations l
WHERE d.`location_id`=l.`location_id`
AND city LIKE '_o%';

SELECT COUNT(*) 个数, city
FROM departments d, locations l
WHERE d.`location_id`=l.`location_id`
GROUP BY city;

SELECT department_name, d.manager_id, MIN(salary)
FROM departments d, employees e
WHERE d.`department_id`=e.`department_id`
AND commission_pct IS NOT NULL
GROUP BY department_name, d.manager_id;

SELECT job_title, COUNT(*)
FROM employees e, jobs j
WHERE e.`job_id`=j.`job_id`
GROUP BY job_title
ORDER BY COUNT(*) DESC;

#三表连接
#多表等值连接的结果为多表的交集部分

SELECT last_name, department_name, city
FROM employees e, departments d, locations l
WHERE e.`department_id`=d.`department_id`
AND d.`location_id`=l.`location_id`
AND city LIKE 's%'
ORDER BY department_name DESC;

#2. 非等值连接

#3. 自连接

SELECT e.employee_id, e.last_name, m.employee_id, m.last_name
FROM employees e, employees m
WHERE e.`manager_id`=m.`employee_id`;

#二、sql99语法
/*
语法：
	sealect 查询列表
	from 表1 别名 【连接类型】
	join 表2 别名
	on 连接条件
	【where 筛选条件】
	【group by 分组】
	【having 筛选条件】
	【order by 排序列表】

内连接：inner
外连接：左外：left 【outer】
	右外：right 【outer】
	全外：full 【outer】
交叉连接：cross

*/

#1. 内连接
/*

语法：
select 查询列表
from 表1 别名
inner join 表2 别名
on 连接条件;

*/

#一、等值连接

SELECT last_name, department_name
FROM departments d
INNER JOIN employees e
ON e.`department_id` = d.`department_id`;

SELECT last_name, job_title
FROM employees e
INNER JOIN jobs j
ON e.`job_id` = j.`job_id`
WHERE e.`last_name` LIKE '%e%';

SELECT city, COUNT(*) 部门个数
FROM departments d
INNER JOIN locations l
ON d.`location_id` = l.`location_id`
GROUP BY city
HAVING COUNT(*) > 3;

#二、非等值连接

```

