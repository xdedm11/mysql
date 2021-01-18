# 一、数据库概述

## 1.1 基本命令

> 安装： mysqld –install
>
> 初始化：mysqld --initialize –console
>
> 开启服务：net start mysql
>
> 关闭服务：net stop mysql
>
> 登录mysql：mysql -u root -p // 回车输入密码
>
> mysql远程登录：mysql -h192.168.151.18 -uroot -p444
>
> 修改密码：alter user 'root'@'localhost' identified by 'root';(by 接着的是密码)
>
> 标记删除mysql服务：sc delete mysql
>
> 中止语句： \c

> DBA命令
>
> - 导出数据（在命令窗口中执行）
>
>   - 导出整个库
>
>     mysqldump powernode>D:\powernode.sql -uroot -p333
>
>   - 导出指定数据库当中的指定表
>
>     mysqldump powernode emp>D:\powernode.sql -uroot –p123
>
> - 导入数据
>
>   > create database powernode;
>   > use powernode;
>   > source D:\powernode.sql

## 1.2 表

行：数据（记录）(data)

列：字段(column): 字段名、数据类型(int, varchar)、相关约束

## 1.3 SQL语句分类

- DQL（数据查询）：查询语句(select)

- DML（数据操作）：对数据增删改,insert,delete,update

- DDL（数据定义）：对表结构增删改,create,drop,alter

- TCL（事务控制）：commit,rollback

- DCL（数据控制）：grant,revoke

## 1.4 导入数据

> - 查看数据库：show databases;

+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+

> - 查看当前数据库：select database();
>
> - 查看数据库版本：select version();
>
> - 创建数据库：create database powernode;
>
> - 使用数据库：use powernode;(切换当前数据库)
>
> - 查看当前数据库下表：show tables;
>
> - 初始化数据：source powernode.sql(拖动sql脚本执行)

+---------------------+
| Tables_in_powernode |
+---------------------+
| dept                |
| emp                 |
| salgrade            |
+---------------------+

> - 删除数据库：drop database powernode;
>
> - 查看表结构：desc emp;
>
> - 查看创建表的语句：show create table emp;

# 二、SQL查询语句(DQL)

> select	5
> 		..
> from			1	
> 		..
> where			2
> 		..
> group by		3
> 		..
> having		4
> 		..
> order by		6
> 		..
>
> limit			7
> 		...;

## 2.1 简单查询（DQL）

> - select 字段1,字段2   from 表名;
>
> - 查询全部字段：select * from emp;
>
> ​	PS: select语句不区分大小写，以;结尾，字段可以参与数学运算
>
> - 给查询结果的列重命名：select sal*12 as yearsal from emp;	//有中文用' '包围，as可以以空格代替
>
> - 查询结果去重：select distinct job from emp;
>
>   distinct只能出现在所有字段最前面。distinct可以多字段联合去重

## 2.2 条件查询

> - select 字段1,字段2   from 表名 **where** 条件;
>
> eg：select ename from emp where sal=5000;
>
> ​		select sal from emp where ename='SMITH';	//不等于<> !=
>
> - select ename,sal from emp where sal between 1100 and 3000;	
>
> ​		//between闭区间，也可以用于字符串首字母匹配（左闭右开）
>
> - select ename from emp where comm is null;	//is not null
>
>   NULL 空
>
> - 逻辑运算 and,or     //and 运算优先级大于or
>
>   select ename,sal,deptno from emp where sal>1000 and (depton=20 or depton=30);
>
> - in, not in
>
>   select ename,job from emp where job in('SALESMAN','MANAGER');
>
> - like(模糊查询)  //%表示任意多个字符，_表示任意一个字符
>
>   select ename from emp where ename like '%o%';
>
>   select ename from emp where ename like '%\-%';	//转义

## 2.3 排序

> - select ename,sal from emp order by sal;

+--------+---------+
| ename  | sal     |
+--------+---------+
| SMITH  |  800.00 |
| JAMES  |  950.00 |
| ADAMS  | 1100.00 |
| WARD   | 1250.00 |
| MARTIN | 1250.00 |
| MILLER | 1300.00 |
| TURNER | 1500.00 |
| ALLEN  | 1600.00 |
| CLARK  | 2450.00 |
| BLAKE  | 2850.00 |
| JONES  | 2975.00 |
| SCOTT  | 3000.00 |
| FORD   | 3000.00 |
| KING   | 5000.00 |
+--------+---------+

> - select ename,sal from emp order by sal desc;
>
>   默认升序排列,asc表示升序，desc表示降序
>
>   order by 2(第二个字段)
>
> 工资相同时按名字升序排:
>
> - select ename,sal from emp order by sal desc,ename asc;

## 2.3 单行处理函数

> select ename, (sal+comm)*12 as yearsal from emp;
>
> 数学表达式中有NULL结果必为NULL

> 空处理函数ifnull()
>
> select ename, (sal+ifnull(comm,0))*12 as yearsal from emp;

## 2.4 分组函数(多行处理函数)

> 分组函数自动忽略NULL
>
> count 计数
> sum 求和
> avg 平均值
> max 最大值
> min 最小值

> 找出工资总和:select sum(sal) from emp;
> 找出最高工资:select max(sal) from emp;
> 找出最低工资:select min(sal) from emp;
> 找出平均工资:select avg(sal) from emp;
> 找出总人数:select count(*) from emp; select count(ename) from emp;
>
> 组合使用：select count(*),sum(sal),avg(sal),max(sal) from emp;

> **分组函数不可直接使用在where子句中**(group by在where之后执行)
>
> 找出工资高于平均工资的员工：select ename,sal from emp where sal>(select avg(sal) from emp);

- group by 与 having

> - group by: 按照某个、某些字段进行分组
>
> - having：对分组后数据进行再次过滤

> 找出每个工作岗位最高薪资：select job,max(sal) from emp group by job;
>
> **当一条语句中有group by时，select后只能跟分组函数和参与分组的字段**

+----------+
| max(sal) |
+----------+
|  1300.00 |
|  1600.00 |
|  2975.00 |
|  3000.00 |
|  5000.00 |
+----------+

> - 多个字段联合分组
>
> 找出每个部门不同工作岗位最高薪资：select deptno, job,max(sal)  from emp group by deptno, job;

+--------+-----------+----------+
| deptno | job       | max(sal) |
+--------+-----------+----------+
|     20 | CLERK     |  1100.00 |
|     30 | SALESMAN  |  1600.00 |
|     20 | MANAGER   |  2975.00 |
|     30 | MANAGER   |  2850.00 |
|     10 | MANAGER   |  2450.00 |
|     20 | ANALYST   |  3000.00 |
|     10 | PRESIDENT |  5000.00 |
|     30 | CLERK     |   950.00 |
|     10 | CLERK     |  1300.00 |
+--------+-----------+----------+

> 找出每个部门最高薪资，要求显示大于2900的数据：
>
> ​	select max(sal),deptno from emp group by deptno having max(sal)>2900;	//此时having效率较低
>
> ​	select max(sal),depton from emp where sal >2900 group by deptno;	//where先执行，故效率较高
>
> 找出每个部门平均薪资，要求显示大于2000的数据：
>
> ​	select avg(sal),deptno from emp group by deptno having avg(sal)>2000;
>
> ​	**此时where句中有avg，所以不能用where**

+-------------+--------+
| avg(sal)    | deptno |
+-------------+--------+
| 2175.000000 |     20 |
| 2916.666667 |     10 |
+-------------+--------+

## 2.5 连接查询

多张表联合查询取出最终结果

> 分类：
>
> - 年代：SQL92(很少用)、SQL99
> - 表的连接方式：
>   - 内连接
>     - 等值连接
>     - 非等值连接:between and
>     - 自连接
>   - 外连接（居多）
>     - 左连接
>     - 右连接
>   - 全连接（很少用）

> 笛卡尔（乘积）现象	(没有条件限制14x4条数据)
>
> > eg：找出每个员工的部门名称，要求显示员工名和部门名
> >
> > select ename,dname from emp,dept;
>
> > 表的别名：（执行效率高，可读性好）
> >
> > select e.ename,d.dname from emp e,dept d ;
>
> > 避免笛卡尔现象
> >
> > select e.ename,d.dname from emp e,dept d where e.deptno=d.deptno;(SQL92)

+--------+------------+
| ename  | dname      |
+--------+------------+
| SMITH  | RESEARCH   |
| ALLEN  | SALES      |
| WARD   | SALES      |
| JONES  | RESEARCH   |
| MARTIN | SALES      |
| BLAKE  | SALES      |
| CLARK  | ACCOUNTING |
| SCOTT  | RESEARCH   |
| KING   | ACCOUNTING |
| TURNER | SALES      |
| ADAMS  | RESEARCH   |
| JAMES  | SALES      |
| FORD   | RESEARCH   |
| MILLER | ACCOUNTING |
+--------+------------+

### 2.5.1 内连接（inner可以省略）

1. 等值连接：连接条件是等量关系

   > select e.ename,d.dname from emp e,dept d where e.deptno=d.deptno;
   >
   > select e.ename,d.dname from emp e **inner join** dept d **on** e.deptno=d.deptno;	//SQL99：on与where分离（表连接条件与数据过滤条件分离）

2. 非等值连接：连接条件是非等量关系

   > eg：找出每个员工的工资等级，要求显示员工名、工资、工资等级
   >
   > select e.ename,e.sal,s.grade from emp e join salgrade s on e.sal between s.losal and s.hisal;

   +--------+---------+-------+
   | ename  | sal     | grade |
   +--------+---------+-------+
   | SMITH  |  800.00 |     1 |
   | ALLEN  | 1600.00 |     3 |
   | WARD   | 1250.00 |     2 |
   | JONES  | 2975.00 |     4 |
   | MARTIN | 1250.00 |     2 |
   | BLAKE  | 2850.00 |     4 |
   | CLARK  | 2450.00 |     4 |
   | SCOTT  | 3000.00 |     4 |
   | KING   | 5000.00 |     5 |
   | TURNER | 1500.00 |     3 |
   | ADAMS  | 1100.00 |     1 |
   | JAMES  |  950.00 |     1 |
   | FORD   | 3000.00 |     4 |
   | MILLER | 1300.00 |     2 |
   +--------+---------+-------+

3. 自连接：一张表看作两张表互相连接

   >找出每个员工的上级领导，要求显示员工名和对应领导名
   >
   > select a.ename as worker,b.ename as boss from emp a join emp b on a.mgr=b.empno;

   +--------+-------+
   | worker | boss  |
   +--------+-------+
   | SMITH  | FORD  |
   | ALLEN  | BLAKE |
   | WARD   | BLAKE |
   | JONES  | KING  |
   | MARTIN | BLAKE |
   | BLAKE  | KING  |
   | CLARK  | KING  |
   | SCOTT  | JONES |
   | TURNER | BLAKE |
   | ADAMS  | SCOTT |
   | JAMES  | BLAKE |
   | FORD   | JONES |
   | MILLER | CLARK |
   +--------+-------+

   13条记录，无KING

### 2.5.2 外连接(outer可省略（已有left,right）)

外连接时，两张表分为主表与副表，主要查询主表中的数据，**副表没有匹配上时自动模拟NULL**

1. 左连接（左边是主表）
2. 右连接（右边是主表）（可相互转化）

> 找出每个员工的上级领导（所有员工）
>
>  select a.ename as worker,b.ename as boss from emp a **left** join emp b on a.mgr=b.empno;
>
>  select a.ename as worker,b.ename as boss from emp b **right** join emp a on a.mgr=b.empno;

+--------+-------+
| worker | boss  |
+--------+-------+
| SMITH  | FORD  |
| ALLEN  | BLAKE |
| WARD   | BLAKE |
| JONES  | KING  |
| MARTIN | BLAKE |
| BLAKE  | KING  |
| CLARK  | KING  |
| SCOTT  | JONES |
| KING   | NULL  |
| TURNER | BLAKE |
| ADAMS  | SCOTT |
| JAMES  | BLAKE |
| FORD   | JONES |
| MILLER | CLARK |
+--------+-------+

14条记录

> 找出哪个部门没有员工
>
> select d.* from emp e right join dept d on e.deptno=d.deptno where e.empno is null;

+--------+------------+--------+
| DEPTNO | DNAME      | LOC    |
+--------+------------+--------+
|     40 | OPERATIONS | BOSTON |
+--------+------------+--------+

部门40没有员工

### 2.5.3 全连接（很少用）

### 2.5.4 三张表连接查询

> 找出每个员工的部门名称以及工资等级
>
> select e.ename,d.dname,s.grade from emp e join dept d on e.deptno=d.deptno join salgrade s on e.sal between s.losal and s.hisal;

+--------+------------+-------+
| ename  | dname      | grade |
+--------+------------+-------+
| SMITH  | RESEARCH   |     1 |
| ALLEN  | SALES      |     3 |
| WARD   | SALES      |     2 |
| JONES  | RESEARCH   |     4 |
| MARTIN | SALES      |     2 |
| BLAKE  | SALES      |     4 |
| CLARK  | ACCOUNTING |     4 |
| SCOTT  | RESEARCH   |     4 |
| KING   | ACCOUNTING |     5 |
| TURNER | SALES      |     3 |
| ADAMS  | RESEARCH   |     1 |
| JAMES  | SALES      |     1 |
| FORD   | RESEARCH   |     4 |
| MILLER | ACCOUNTING |     2 |
+--------+------------+-------+
14 rows in set (0.00 sec)

> 找出每个员工的部门名称,工资等级和上级领导
>
> select e.ename '员工',d.dname,s.grade,e1.ename '领导' from emp e join dept d on e.deptno=d.deptno join salgrade s on e.sal between s.losal and s.hisal left join emp e1 on e.mgr=e1.empno;

+--------+------------+-------+-------+
| 员工   | dname      | grade | 领导  |
+--------+------------+-------+-------+
| SMITH  | RESEARCH   |     1 | FORD  |
| ALLEN  | SALES      |     3 | BLAKE |
| WARD   | SALES      |     2 | BLAKE |
| JONES  | RESEARCH   |     4 | KING  |
| MARTIN | SALES      |     2 | BLAKE |
| BLAKE  | SALES      |     4 | KING  |
| CLARK  | ACCOUNTING |     4 | KING  |
| SCOTT  | RESEARCH   |     4 | JONES |
| KING   | ACCOUNTING |     5 | **NULL**  |
| TURNER | SALES      |     3 | BLAKE |
| ADAMS  | RESEARCH   |     1 | SCOTT |
| JAMES  | SALES      |     1 | BLAKE |
| FORD   | RESEARCH   |     4 | JONES |
| MILLER | ACCOUNTING |     2 | CLARK |
+--------+------------+-------+-------+
14 rows in set (0.00 sec)

## 2.6 子查询

被嵌套的select语句，可以出现在select、from、where中

### 2.6.1 where子句

> 找出高于平均薪资的员工信息
>
> select *from emp where sal>(select avg(sal) from emp);

+-------+-------+-----------+------+------------+---------+------+--------+
| EMPNO | ENAME | JOB       | MGR  | HIREDATE   | SAL     | COMM | DEPTNO |
+-------+-------+-----------+------+------------+---------+------+--------+
|  7566 | JONES | MANAGER   | 7839 | 1981-04-02 | 2975.00 | NULL |     20 |
|  7698 | BLAKE | MANAGER   | 7839 | 1981-05-01 | 2850.00 | NULL |     30 |
|  7782 | CLARK | MANAGER   | 7839 | 1981-06-09 | 2450.00 | NULL |     10 |
|  7788 | SCOTT | ANALYST   | 7566 | 1987-04-19 | 3000.00 | NULL |     20 |
|  7839 | KING  | PRESIDENT | NULL | 1981-11-17 | 5000.00 | NULL |     10 |
|  7902 | FORD  | ANALYST   | 7566 | 1981-12-03 | 3000.00 | NULL |     20 |
+-------+-------+-----------+------+------------+---------+------+--------+

### 2.6.2 from子句

> 找出每个部门平均薪水的薪资等级
>
> 1. 找出每个部门平均薪水
>
>    select deptno,avg(sal) as avgsal from emp group by deptno;
>
>    +--------+-------------+
>    | deptno | avgsal      |
>    +--------+-------------+
>    |     20 | 2175.000000 |
>    |     30 | 1566.666667 |
>    |     10 | 2916.666667 |
>    +--------+-------------+
>    3 rows in set (0.00 sec)
>
> 2. 将上表（临时表）与salgrade连接，找出每个部门平均薪水的薪资等级 
>
>    select t.*,s.grade from (select deptno,avg(sal) as avgsal from emp group by deptno) t join salgrade s on t.avgsal between s.losal and s.hisal;
>
>    +--------+-------------+-------+
>    | deptno | avgsal      | grade |
>    +--------+-------------+-------+
>    |     20 | 2175.000000 |     4 |
>    |     30 | 1566.666667 |     3 |
>    |     10 | 2916.666667 |     4 |
>    +--------+-------------+-------+
>    3 rows in set (0.01 sec)

> 找出每个部门薪资等级的平均值
>
> 1. 找出每个员工薪水等级
>
>    select  e.ename, e.sal, e.deptno, s.grade from emp e join salgrade s on e.sal between s.losal and s.hisal;
>
>    +--------+---------+--------+-------+
>    | ename  | sal     | deptno | grade |
>    +--------+---------+--------+-------+
>    | SMITH  |  800.00 |     20 |     1 |
>    | ALLEN  | 1600.00 |     30 |     3 |
>    | WARD   | 1250.00 |     30 |     2 |
>    | JONES  | 2975.00 |     20 |     4 |
>    | MARTIN | 1250.00 |     30 |     2 |
>    | BLAKE  | 2850.00 |     30 |     4 |
>    | CLARK  | 2450.00 |     10 |     4 |
>    | SCOTT  | 3000.00 |     20 |     4 |
>    | KING   | 5000.00 |     10 |     5 |
>    | TURNER | 1500.00 |     30 |     3 |
>    | ADAMS  | 1100.00 |     20 |     1 |
>    | JAMES  |  950.00 |     30 |     1 |
>    | FORD   | 3000.00 |     20 |     4 |
>    | MILLER | 1300.00 |     10 |     2 |
>    +--------+---------+--------+-------+
>    14 rows in set (0.00 sec)
>
> 2. 按照deptno分组，找出每个部门薪资等级的平均值
>
>    select  e.deptno, avg(s.grade) from emp e join salgrade s on e.sal between s.losal and s.hisal group by e.deptno;
>
>    +--------+--------------+
>    | deptno | avg(s.grade) |
>    +--------+--------------+
>    |     20 |       2.8000 |
>    |     30 |       2.5000 |
>    |     10 |       3.6667 |
>    +--------+--------------+
>    3 rows in set (0.00 sec)

### 2.6.3 select子句

> 找出每个员工所在部门名称，要求显示员工名和部门名
>
> select d.dname from dept d, emp e where e.deptno=d.deptno;
>
> +------------+
> | dname      |
> +------------+
> | RESEARCH   |
> | SALES      |
> | SALES      |
> | RESEARCH   |
> | SALES      |
> | SALES      |
> | ACCOUNTING |
> | RESEARCH   |
> | ACCOUNTING |
> | SALES      |
> | RESEARCH   |
> | SALES      |
> | RESEARCH   |
> | ACCOUNTING |
> +------------+
> 14 rows in set (0.00 sec)
>
> select e.ename,(select d.dname from dept d where e.deptno=d.deptno) as dname from emp e;
>
> +--------+------------+
> | ename  | dname      |
> +--------+------------+
> | SMITH  | RESEARCH   |
> | ALLEN  | SALES      |
> | WARD   | SALES      |
> | JONES  | RESEARCH   |
> | MARTIN | SALES      |
> | BLAKE  | SALES      |
> | CLARK  | ACCOUNTING |
> | SCOTT  | RESEARCH   |
> | KING   | ACCOUNTING |
> | TURNER | SALES      |
> | ADAMS  | RESEARCH   |
> | JAMES  | SALES      |
> | FORD   | RESEARCH   |
> | MILLER | ACCOUNTING |
> +--------+------------+
> 14 rows in set (0.01 sec)

## 2.7 union

将查询结果集相加，可以将两张不相干表中数据拼接显示（字段数相同）

> 找出工作岗位是SALESMAN,MANAGER的员工
>
> select ename,job from emp where job='MANAGER' union select ename,job from emp where job='SALESMAN';
>
> +--------+----------+
> | ename  | job      |
> +--------+----------+
> | JONES  | MANAGER  |
> | BLAKE  | MANAGER  |
> | CLARK  | MANAGER  |
> | ALLEN  | SALESMAN |
> | WARD   | SALESMAN |
> | MARTIN | SALESMAN |
> | TURNER | SALESMAN |
> +--------+----------+
> 7 rows in set (0.00 sec)

## 2.8 limit(分页查询)

mysql特有

limit取结果集中的部分数据

最后执行

> - limit startIndex,length
>
>   startIndex表示起始位置，默认为0；length表示取几个
>
> eg：取出工资前五的员工
>
> ​	select ename,sal from emp order by sal desc limit 0,5;
>
> +-------+---------+
> | ename | sal     |
> +-------+---------+
> | KING  | 5000.00 |
> | SCOTT | 3000.00 |
> | FORD  | 3000.00 |
> | JONES | 2975.00 |
> | BLAKE | 2850.00 |
> +-------+---------+
> 5 rows in set (0.00 sec)

> 每页显示3条记录：
> 第1页：0, 3
> 第2页：3, 3
> 第3页：6, 3
> 第4页：9, 3
> 第5页：12, 3
>
> - 每页显示pageSize条记录：
>   第pageNo页：(pageNo - 1) * pageSize, pageSize
>
>   pageSize是什么？是每页显示多少条记录
>   pageNo是什么？显示第几页
>
> java代码
>
> ```java
> int pageNo = 2; // 页码是2
> int pageSize = 10; // 每页显示10条
> limit (pageNo - 1) * pageSize, pageSize
> ```

# 三、SQL表操作语句DDL、DML

CRUD：create,Retrieve（检索）,update,delete

- DML:insert delete update
- DDL:create drop alter

## 3.1 创建表(create)

表名在数据库当中一般建议以：t_或者tbl_开始。

> create table 表名(
> 			字段名1 数据类型,
> 			字段名2 数据类型,
> 			字段名3 数据类型,
> 			....
> 		);

> - 数据类型
>
> ​		int		整数型(java中的int)
> ​		bigint	长整型(java中的long)
> ​		float		浮点型(java中的float double)
> ​		char		定长字符串(String)
> ​		varchar	可变长字符串(StringBuffer/StringBuilder)
> ​		date		日期类型 （对应Java中的java.sql.Date类型）
> ​		BLOB		二进制大对象（存储图片、视频等流媒体信息） Binary Large OBject （对应java中的Object）（必须使用Java的IO才能插入）
> ​		CLOB		字符大对象（存储较大文本，比如，可以存储4G的字符串。） Character Large OBject（对应java中的Object）
>
> - char和varchar怎么选择？
>
>   - 在实际的开发中，当某个字段中的数据长度不发生改变的时候，是定长的，例如：性别、生日等都是采用char。
>   - 当一个字段的数据长度不确定，例如：简介、姓名等都是采用varchar。
>
> - BLOB和CLOB类型的使用？
>   电影表: t_movie
>
>   id(int)	name(varchar)		playtime(date/char)		haibao(BLOB)		history(CLOB)

> create table t_student(
> 			no bigint,
> 			name varchar(255),
> 			sex char(1),
> 			classno varchar(255),
> 			birth char(10)
> 		);

## 3.2 插入数据(insert)

> - insert into 表名(字段名1,字段名2,字段名3,....) values(值1,值2,值3,....)
>   要求：字段的数量和值的数量相同，并且数据类型要对应相同。
>
>   ​			若字段名缺省时，后面的值必须与表的字段数量、顺序严格对应
>
>   insert into t_student(no,name,sex,classno,birth) values(1,'zhangsan','1','gaosan1ban', '1950-10-12');
>
>   insert into t_student(name,sex,classno,birth,no) values('lisi','1','gaosan1ban', '1950-10-12',2);
>
>   insert into t_student(name) values('wangwu');
>
>   ​	// 除name字段之外，剩下的所有字段自动插入NULL。
>
>   +------+----------+------+------------+------------+
>   | no   | name     | sex  | classno    | birth      |
>   +------+----------+------+------------+------------+
>   |    1 | zhangsan | 1    | gaosan1ban | 1950-10-12 |
>   |    2 | lisi     | 1    | gaosan1ban | 1950-10-12 |
>   | NULL | wangwu   | NULL | NULL       | NULL       |
>   +------+----------+------+------------+------------+
>
> - default
>
>   create table t_student(
>   		no bigint,
>   		name varchar(255),
>   		sex char(1) default 1,
>   		classno varchar(255),
>   		birth char(10)
>   	);
>
>   此时desc t_student;
>
>   +---------+--------------+------+-----+---------+-------+
>   | Field   | Type         | Null | Key | Default | Extra |
>   +---------+--------------+------+-----+---------+-------+
>   | no      | bigint       | YES  |     | NULL    |       |
>   | name    | varchar(255) | YES  |     | NULL    |       |
>   | sex     | char(1)      | YES  |     | 1       |       |
>   | classno | varchar(255) | YES  |     | NULL    |       |
>   | birth   | char(10)     | YES  |     | NULL    |       |
>   +---------+--------------+------+-----+---------+-------+
>
> - 插入多行数据
>
>   insert into t_student
>   		(no,name,sex,classno,birth) 
>   	values
>   		(3,'rose','1','gaosi2ban','1952-12-14'),(4,'laotie','1','gaosi2ban','1955-12-14');

## 3.3 删除表(drop)

> - drop table 表名;
> - drop table if exists t_student; // 当这个表存在的话删除。

## 3.4 复制表

> - create table 表名 as select语句;
>
>   将查询结果复制到一张表中
>
>   create table emp1 as select *from emp;
>
> - insert into 表名 select语句;
>
>   将查询结果插入数据
>
>   insert into dep1 select* from dept;

## 3.5 修改表中数据(update)

> - update 表名 set 字段名1=值1**,**字段名2=值2... where 条件;
>
>    update dept1 set loc='SHANGHAI',dname='RENSHIBU' where deptno=10;
>
>   不加where默认修改所有数据

## 3.6 删除数据(delete,truncate)

> - delete from 表名 where 条件;
>
>   delete from dept1 where deptno=10;
>
>   不加where默认删除所有数据
>
> - truncate table emp1;
>
>   删除表中所有数据；表被截断不可回滚，永久丢失。

## 3.7 修改表结构(alter) 工具

## 3.8 约束

> - 非空约束(not null)：约束的字段不能为NULL
> - 唯一约束(unique)：约束的字段不能重复
> - 主键约束(primary key)：约束的字段既不能为NULL，也不能重复（简称PK）
> - 外键约束(foreign key)：...（简称FK）
> - 检查约束(check)：注意Oracle数据库有check约束，但是mysql没有，目前mysql不支持该约束。

### 3.8.1 非空约束(not null)

> drop table if exists t_user;
> create table t_user(
> 	id int,
> 	username varchar(255) **not null**,
> 	password varchar(255)
> );
> insert into t_user(id,username,password) values(1,'lisi','123');

### 3.8.2 唯一性约束(unique)

唯一约束修饰的字段具有唯一性，不能重复。但可以为NULL。

> - drop table if exists t_user;
>
> - create table t_user(
>   		id int,
>     		username varchar(255) **unique**  //**【列级约束】**
>
>   );
>
> - insert into t_user values(1,'zhangsan');
>
> -  insert into t_user(id) values(2);
>
> +------+----------+
> | id   | username |
> +------+----------+
> |    1 | zhangsan |
> |    2 | NULL     |
> +------+----------+

> 给多个列同时加unique（联合唯一）
>
> create table t_user(
> 		id int, 
> 		usercode varchar(255),
> 		username varchar(255),
> 		**unique(usercode,username)** // 多个字段联合起来添加1个约束unique **【表级约束】**
> );

### 3.8.3 主键约束(PK)

PK约束修饰字段不能重复，也不能为NULL

 一张表的主键约束只能有1个。

> create table t_user(
> 		id int **primary key**, 
> 		username varchar(255),
> 		email varchar(255)
> );

> 主键约束、主键字段、主键值
>
> 主键作用：
>
> - 表的设计三范式中有要求，第一范式就要求任何一张表都应该有主键。
> - 主键值是这行记录在这张表当中的唯一标识。

> 主键的分类：
>
> - 根据主键字段数目：
>   - 单一主键
>   - 复合主键（违背三范式）
> - 主键性质：
>   - 自然主键：主键值最好就是一个和业务没有任何关系的自然数。（推荐）
>   - 业务主键：主键值和系统的业务挂钩（业务一旦发生改变的时候，主键值可能也需要随着发生变化，但有的时候没有办法变化，因为变化可能会导致主键值重复。）

> 使用表级约束方式定义主键：
>
> drop table if exists t_user;
> create table t_user(
> 	id int,
> 	username varchar(255),
> 	**primary key(id)**
> );
> insert into t_user(id,username) values(1,'zs');
> insert into t_user(id,username) values(2,'ls');
> insert into t_user(id,username) values(3,'ws');
> insert into t_user(id,username) values(4,'cs');
> select * from t_user;

> **主键值自增**
>
> create table t_user(
> 	id int primary key **auto_increment**, // id字段自动维护一个自增的数字，从1开始，以1递增。
> 	username varchar(255)
> );

### 3.8.4 外键约束(FK)

外键可以为NULL

外键字段引用其他表的某个字段的时候，被引用的字段不一定是主键，但至少具有unique约束。

> 数据库表，用来维护学生和班级的信息
>
> 1. 一张表存储所有数据（冗余）
>
> 2. 两张表（班级表和学生表）
>
>    - t_class
>
>      cno(pk)		cname
>
>      ​	101		北京大兴区经济技术开发区亦庄二中高三1班
>      ​	102		北京大兴区经济技术开发区亦庄二中高三2班
>
>    - t_student
>
>      sno(pk)		sname		classno(该字段添加**外键约束fk**)
>
>      ​    1				zs1				101
>      ​	2				zs2				101
>      ​	3				zs3				102
>      ​	4				zs4				102
>      ​	5				zs5				102
>
> t_student中的classno字段引用t_class表中的cno字段，此时t_student表叫做子表。t_class表叫做父表。
>
> create table t_class(cno int,cname varchar(255),primary key(cno));
>
> create table t_student(sno int,sname varchar(255),classno int,primary key(sno),**foreign key**(classno) **references** t_class(cno));
>
> 		insert into t_class values(101,'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx');
> 		insert into t_class values(102,'yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy');
> 		insert into t_student values(1,'zs1',101);
> 		insert into t_student values(2,'zs2',101);
> 		insert into t_student values(3,'zs3',102);
> 		insert into t_student values(4,'zs4',102);
> 		insert into t_student values(5,'zs5',102);
> 		insert into t_student values(6,'zs6',102);
> 		select * from t_class;
> 		select * from t_student;

> 顺序要求：
>
> - 删除数据的时候，先删除子表，再删除父表。
> - 添加数据的时候，先添加父表，在添加子表。
> - 创建表的时候，先创建父表，再创建子表。
> - 删除表的时候，先删除子表，在删除父表。

# 四、存储引擎

 ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci 

> mysql默认使用的存储引擎是InnoDB方式。
> 默认采用的字符集是UTF8

> 查看当前mysql支持的存储引擎
>
> show engines \G

常见存储引擎

## 4.1 MyISAM

```text
Engine: MyISAM
Support: YES
Comment: MyISAM storage engine
Transactions: NO(事务)
          XA: NO
Savepoints: NO
```
- MyISAM这种存储引擎不支持事务。
- MyISAM是mysql最常用的存储引擎，但是这种引擎不是默认的。
- MyISAM采用三个文件组织一张表：
  - xxx.frm（存储**格式**的文件）
  - xxx.MYD（存储表中**数据**的文件）
  - xxx.MYI（存储表中**索引**的文件）
- 优点：可被压缩，节省存储空间。并且可以转换为只读表，提高检索效率。
  缺点：不支持事务。

## 4.2 InnoDB

    Engine: InnoDB
    Support: DEFAULT
    Comment: Supports transactions, row-level locking, and foreign keys
    Transactions: YES
              XA: YES
    Savepoints: YES
优点：支持事务、行级锁、外键等。这种存储引擎数据的安全得到保障。
表的结构存储在xxx.frm文件中
数据存储在tablespace这样的表空间中（逻辑概念），无法被压缩，无法转换成只读。
这种InnoDB存储引擎在MySQL数据库崩溃之后提供自动恢复机制。
InnoDB支持级联删除和级联更新。		

## 4.3 MEMORY

      Engine: MEMORY
     Support: YES
     Comment: Hash based, stored in memory, useful for temporary tables
     Transactions: NO
              XA: NO
      Savepoints: NO
缺点：不支持事务。数据容易丢失。因为所有数据和索引都是存储在内存当中的。
优点：查询速度最快。
			以前叫做HEPA引擎。

# 五、事务(Transaction)

## 5.1 定义

一个事务是一个完整的业务逻辑单元，不可再分。

比如：银行账户转账，从A账户向B账户转账10000.需要执行两条update语句：
			update t_act set balance = balance - 10000 where actno = 'act-001';
			update t_act set balance = balance + 10000 where actno = 'act-002';

以上两条DML语句必须同时成功，或者同时失败，不允许出现一条成功，一条失败。

要想保证以上的两条DML语句同时成功或者同时失败，那么就需要使用数据库的“事务机制”。

## 5.2 相关语句：DML

insert,delete,update

## 5.3 一个事务需要多条DML语句共同联合完成

## 5.4 事务原理

- 开启事务
- insert,update等操作只记录历史操作，不真正修改硬盘数据
- 提交事务(commit)或回滚事务(rollback)，修改硬盘数据（提交时），清空历史缓存

## 5.5 特性(ACID)

- A: 原子性：事务是最小的工作单元，不可再分。
- C: 一致性：事务必须保证多条DML语句同时成功或者同时失败。
- I：隔离性：事务A与事务B之间具有隔离。
- D：持久性：持久性说的是最终数据必须持久化到硬盘文件中，事务才算成功的结束。

> 隔离级别（墙不断加厚）
>
> - 读未提交（read uncommitted）：
>
>   对方事务还没有提交，我们当前事务可以读取到对方未提交的数据。	
>
>   读未提交存在脏读（Dirty Read）现象：表示读到了脏的数据。（不稳定、缓存中的数据）
>
> - 读已提交（read committed）
>
>   对方事务提交之后的数据我方可以读取到。
>   这种隔离级别解决了脏读现象。
>   读已提交存在的问题是：不可重复读。
>
> - 可重复读（repeatable read）
>
>   这种隔离级别解决了：不可重复读问题。（重复读取得到的数据一直不变，不管其他人是否修改数据）
>   这种隔离级别存在的问题是：读取到的数据是幻象。
>
> - 序列化读/串行化读（serializable） 
>
>   解决了所有问题。多个事务不能并发
>   效率低。需要事务排队。
>
> oracle数据库默认的隔离级别是：读已提交。
> mysql数据库默认的隔离级别是：可重复读。

## 5.6 演示事务

mysql事务默认情况下是自动提交的。（什么是自动提交？只要执行任意一条DML语句则提交一次。）

关闭自动提交：start transaction;

> - 准备表
>
>   drop table if exists t_user;
>   create table t_user(id int primary key auto_increment,username varchar(255));
>
> - mysql中的事务是支持自动提交的，只要执行一条DML，则提交一次。（无法回滚）
>
> - 使用start transaction;关闭自动提交机制。(commit或rollback之后得重新开启事务)
>
>   >  insert into t_user(username) values('lisi');
>   >
>   >  insert into t_user(username) values('wangwu');
>   >
>   > rollback;
>   >
>   > Empty set (0.00 sec)
>
> - commit之后无法回滚
>
>   > start transaction;
>   >
>   > insert into t_user(username) values('wangwu');
>   >
>   > insert into t_user(username) values('rose');
>   >
>   > insert into t_user(username) values('jack');
>   >
>   > commit;
>   >
>   > +----+----------+
>   > | id | username |
>   > +----+----------+
>   > |  3 | wangwu   |
>   > |  4 | rose     |
>   > |  5 | jack     |
>   > +----+----------+
>   > 3 rows in set (0.00 sec)
>   >
>   > rollback;
>   >
>   > +----+----------+
>   > | id | username |
>   > +----+----------+
>   > |  3 | wangwu   |
>   > |  4 | rose     |
>   > |  5 | jack     |
>   > +----+----------+
>   > 3 rows in set (0.00 sec)

使用两个事务演示以上隔离级别

> - select @@global.transaction_isolation;    //查询事务隔离级别
>
>   +--------------------------------+
>   | @@global.transaction_isolation |
>   +--------------------------------+
>   | READ-UNCOMMITTED               |
>   +--------------------------------+
>
> - read uncommitted
>
>   set global transaction isolation level read uncommitted;
>
> - read committed;
>
>   set global transaction isolation level read committed;
>
> - repeatable read;
>
>   set global transaction isolation level repeatable read;
>
> - serializable
>
>   set global transaction isolation level serializable;
>
>   //一个事务没有结束时另一个事务查询会卡住

# 六、索引

## 6.1 索引作用

相当于一本书的目录，通过目录可以快速的找到对应的资源。(根本原理是缩小了扫描的范围)

表中的数据经常被修改就不适合添加索引，因为数据一旦修改，索引需要重新排序，进行维护。

模糊查询的时候，第一个通配符使用的是%，这个时候索引是失效的。

> 数据库方面，查询一张表的时候有两种检索方式：
>
> - 第一种方式：全表扫描
> - 第二种方式：根据索引检索（效率很高）

> select ename,sal from emp where ename = 'SMITH';
> 当ename字段上没有添加索引的时候，以上sql语句会进行全表扫描，扫描ename字段中所有的值。
> 当ename字段上添加索引的时候，以上sql语句会根据索引扫描，快速定位。

## 6.2 创建、删除索引对象

- 创建索引对象：
  			create index 索引名称 on 表名(字段名);

- 删除索引对象：
  			drop index 索引名称 on 表名;

## 6.3 什么时候考虑给字段添加索引

**主键和具有unique约束的字段自动会添加索引。**根据主键查询效率较高。尽量根据主键检索。

- 数据量庞大。（根据客户的需求，根据线上的环境）
- 该字段很少的DML操作。（因为字段进行修改操作，索引也需要维护）
- 该字段经常出现在where子句中。（经常根据哪个字段查询）

## 6.4 演示

> - explain select ename,sal from emp where sal = 5000;	
>
>   //查询sql语句执行计划。扫描方式——全表扫描
>
> +----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
> | id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
> +----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
> |  1 | SIMPLE      | emp   | NULL       | **ALL**  | NULL          | NULL | NULL    | NULL |   **14** |    10.00 | Using where |
> +----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
> 1 row in set, 1 warning (0.01 sec)
>
> - create index emp_sal_index on emp(sal);
>
>   +----+-------------+-------+------------+------+---------------+---------------+---------+-------+------+----------+-------+
>   | id | select_type | table | partitions | type | possible_keys | key           | key_len | ref   | rows | filtered | Extra |
>   +----+-------------+-------+------------+------+---------------+---------------+---------+-------+------+----------+-------+
>   |  1 | SIMPLE      | emp   | NULL       | ref  | **emp_sal_index** | emp_sal_index | 9       | const |    **1** |   100.00 | NULL  |
>   +----+-------------+-------+------------+------+---------------+---------------+---------+-------+------+----------+-------+
>   1 row in set, 1 warning (0.01 sec)
>
> - drop index emp_sal_index on  emp;

## 6.5 索引底层数据结构 B + Tree

通过B Tree缩小扫描范围，底层索引进行了排序，分区，索引会携带数据在表中的“物理地址”，最终通过索引检索到数据之后，获取到关联的物理地址，通过物理地址定位表中的数据，效率是最高的。

## 6.6 索引分类

- 单一索引：给单个字段添加索引
- 复合索引: 给多个字段联合起来添加1个索引
- 主键索引：主键上会自动添加索引
- 唯一索引：有unique约束的字段上会自动添加索引

# 七、视图

## 7.1 什么是视图

站在不同的角度去看到数据。（同一张表的数据，通过不同的角度去看待）

## 7.2 创建、删除视图

只有DQL语句才能以视图对象的方式创建出来。

> create view myview as select empno,ename from emp;	//as后只能跟select语句
> drop view myview;

## 7.3 面向视图操作

对视图进行增删改查，会影响到原表数据。可以对视图进行CRUD操作。

> - select * from myview;
>
> - CRUD
>
>   create table emp_bak as select * from emp;
>
>   create view myview1 as select empno,ename,sal from emp_bak;
>
>   update myview1 set ename='hehe',sal=1 where empno = 7369; // 通过视图修改原表数据。
>
>   delete from myview1 where empno = 7369; // 通过视图删除原表数据。

## 7.4 视图作用

视图可以隐藏表的实现细节。

保密级别较高的系统，数据库只对外提供相关的视图，java程序员只对视图对象进行CRUD。

# 八、数据库设计三范式

## 8.1 设计范式

设计表的依据。按照这个三范式设计的表不会出现数据冗余。

## 8.2 三范式

- 第一范式：任何一张表都应该有主键，并且每一个字段原子性不可再分。

- 第二范式：建立在第一范式的基础之上，

  ​					所有非主键字段完全依赖主键，不能产生部分依赖。(复合主键时易出现这种情况)

- 第三范式：建立在第二范式的基础之上，所有非主键字段直接依赖主键，不能产生传递依赖。

  > **多对多？三张表，关系表两个外键。**（不符合第二范式） 
  >
  > - t_student学生表
  >   sno(pk)		sname
  >
  > -------------------
  > ​			1				张三
  > ​			2				李四
  > ​			3				王五
  >
  > - t_teacher 讲师表
  >   			tno(pk)		tname
  >
  >   ---------------------
  >   ​			1				王老师
  >   ​			2				张老师
  >   ​			3				李老师
  >
  > - t_student_teacher_relation 学生讲师关系表
  >   			id(pk)		sno(fk)		tno(fk)
  >
  >   ----------------------------------
  >   ​			1				1				3
  >   ​			2				1				1
  >   ​			3				2				2
  >   ​			4				2				3
  >   ​			5				3				1
  >   ​			6				3				3

  > 　　**一对多？两张表，多的表加外键。**（不符合第三范式）
  >
  > 　- 班级t_class
  >
  > 　 			cno(pk)			cname
  >
  > --------------------------
  > 　 ​			1					班级1
  > 　 ​			2					班级2
  >
  > 　- 学生t_student
  >
  > 　 			sno(pk)			sname				classno(fk)
  >
  > ---------------------------------------------
  >
  > 　 ​			101				张1				1
  > 　 ​			102				张2				1
  > 　 ​			103				张3				2
> 　 ​			104				张4				2
  > 　 ​			105				张5				2

  > 一对一设计方案
  >
  > - 主键共享
  >
  >   - t_user_login  用户登录表
  >     id(pk)		username			password
  >
  >   --------------------------------------
  >   ​			1				zs					123
  >   ​			2				ls					456
  >
  >   - t_user_detail 用户详细信息表
  >     id(**pk+fk**)	realname			tel			....
  >
  >     ------------------------------------------------
  >     ​			1				张三				1111111111
  >     ​			2				李四				1111415621
  >
  > - 外键唯一
  >
  >   - t_user_login  用户登录表
  >     			id(pk)		username			password
  >
  >     --------------------------------------
  >     ​			1				zs					123
  >     ​			2				ls					456
  >
  >   - t_user_detail 用户详细信息表
  >     			id(pk)	   realname			tel				userid(**fk**+unique)....
  >
  >     -----------------------------------------------------------
  >     ​			1				张三				1111111111		2
  >     ​			2				李四				1111415621		1

# 作业

1. 取得每个部门最高薪水的人员名称

   > - 每个部门最高薪水
   >
   >   select deptno,max(sal) as maxsal from emp group by deptno;
   >
   >   +--------+---------+
   >   | deptno | maxsal  |
   >   +--------+---------+
   >   |     20 | 3000.00 |
   >   |     30 | 2850.00 |
   >   |     10 | 5000.00 |
   >   +--------+---------+
   >
   > - 将以上结果作为临时表t和emp连接，条件是t.deptno=e.deptno and t.maxsal=e.sal
   >
   >   select e.ename,t.* from (select deptno,max(sal) as maxsal from emp group by deptno) t join emp e on t.deptno=e.deptno and t.maxsal=e.sal;
   >
   >   +-------+--------+---------+
   >   | ename | deptno | maxsal  |
   >   +-------+--------+---------+
   >   | BLAKE |     30 | 2850.00 |
   >   | SCOTT |     20 | 3000.00 |
   >   | KING  |     10 | 5000.00 |
   >   | FORD  |     20 | 3000.00 |
   >   +-------+--------+---------+
   >   4 rows in set (0.00 sec)

2. 哪些人的薪水在部门的平均薪水之上

   > - 部门平均薪水
   >
   >   select deptno,avg(sal) as avgsal from emp group by deptno;
   >
   >   +--------+-------------+
   >   | deptno | avgsal      |
   >   +--------+-------------+
   >   |     20 | 2175.000000 |
   >   |     30 | 1566.666667 |
   >   |     10 | 2916.666667 |
   >   +--------+-------------+
   >
   > - 将以上结果作为临时表t和emp连接，条件是t.avgsal<e.sal and t.deptno =e.deptno
   >
   >   select t.*,e.ename,e.sal from emp e join (select deptno,avg(sal) as avgsal from emp group by deptno) t on t.avgsal<e.sal and t.deptno =e.deptno;
   >
   >   +--------+-------------+-------+---------+
   >   | deptno | avgsal      | ename | sal     |
   >   +--------+-------------+-------+---------+
   >   |     30 | 1566.666667 | ALLEN | 1600.00 |
   >   |     20 | 2175.000000 | JONES | 2975.00 |
   >   |     30 | 1566.666667 | BLAKE | 2850.00 |
   >   |     20 | 2175.000000 | SCOTT | 3000.00 |
   >   |     10 | 2916.666667 | KING  | 5000.00 |
   >   |     20 | 2175.000000 | FORD  | 3000.00 |
   >   +--------+-------------+-------+---------+
   >   6 rows in set (0.01 sec)

3. 取得部门中（所有人的）平均的薪水等级，如下：

   > - 每个人的薪水等级
   >
   >   select e.deptno,e.sal,e.ename,s.grade from emp e join salgrade s on e.sal between s.losal and s.hisal;
   >
   >   +--------+---------+--------+-------+
   >   | deptno | sal     | ename  | grade |
   >   +--------+---------+--------+-------+
   >   |     20 |  800.00 | SMITH  |     1 |
   >   |     30 | 1600.00 | ALLEN  |     3 |
   >   |     30 | 1250.00 | WARD   |     2 |
   >   |     20 | 2975.00 | JONES  |     4 |
   >   |     30 | 1250.00 | MARTIN |     2 |
   >   |     30 | 2850.00 | BLAKE  |     4 |
   >   |     10 | 2450.00 | CLARK  |     4 |
   >   |     20 | 3000.00 | SCOTT  |     4 |
   >   |     10 | 5000.00 | KING   |     5 |
   >   |     30 | 1500.00 | TURNER |     3 |
   >   |     20 | 1100.00 | ADAMS  |     1 |
   >   |     30 |  950.00 | JAMES  |     1 |
   >   |     20 | 3000.00 | FORD   |     4 |
   >   |     10 | 1300.00 | MILLER |     2 |
   >   +--------+---------+--------+-------+
   >
   > - 部门中（所有人的）平均的薪水等级
   >
   >   select e.deptno,avg(s.grade) from emp e join salgrade s on e.sal between s.losal and s.hisal group by e.deptno;
   >
   >   +--------+--------------+
   >   | deptno | avg(s.grade) |
   >   +--------+--------------+
   >   |     20 |       2.8000 |
   >   |     30 |       2.5000 |
   >   |     10 |       3.6667 |
   >   +--------+--------------+
   >   3 rows in set (0.00 sec)

4. 不准用组函数（Max），取得最高薪水

   > - 降序
   >
   >   select ename,sal from emp order by sal desc limit 1;
   >
   >   +-------+---------+
   >   | ename | sal     |
   >   +-------+---------+
   >   | KING  | 5000.00 |
   >   +-------+---------+
   >   1 row in set (0.00 sec)
   >
   > - 表的自连接
   >
   >   select distinct a.sal from emp a join emp b on a.sal<b.sal;
   >
   >   +---------+
   >   | sal     |
   >   +---------+
   >   | 1600.00 |
   >   | 1250.00 |
   >   | 2975.00 |
   >   | 2850.00 |
   >   | 2450.00 |
   >   | 3000.00 |
   >   | 5000.00 |
   >   | 1500.00 |
   >   | 1100.00 |
   >   |  950.00 |
   >   | 1300.00 |
   >   +---------+
   >
   >   select sal from emp where sal not in (select distinct a.sal from emp a join emp b on a.sal<b.sal);
   >
   >   +---------+
   >   | sal     |
   >   +---------+
   >   | 5000.00 |
   >   +---------+
   >   1 row in set (0.01 sec)

5. 取得平均薪水最高的部门的部门编号

   > - 各部门的平均薪水
   >
   >   select deptno, avg(sal) as avgsal from emp group by deptno;
   >
   >   +--------+-------------+
   >   | deptno | avgsal      |
   >   +--------+-------------+
   >   |     20 | 2175.000000 |
   >   |     30 | 1566.666667 |
   >   |     10 | 2916.666667 |
   >   +--------+-------------+
   >   3 rows in set (0.00 sec)
   >
   > - select deptno, avg(sal) as avgsal from emp group by deptno order by avgsal desc limit 1;
   >
   >   +--------+-------------+
   >   | deptno | avgsal      |
   >   +--------+-------------+
   >   |     10 | 2916.666667 |
   >   +--------+-------------+
   >   1 row in set (0.00 sec)

   > 第二步用max
   >
   > select deptno, avg(sal) as avgsal from emp group by deptno;
   >
   > select max(t.avgsal) from (select deptno, avg(sal) as avgsal from emp group by deptno) t;
   >
   > +---------------+
   > | max(t.avgsal) |
   > +---------------+
   > |   2916.666667 |
   > +---------------+
   > 1 row in set (0.00 sec)

6. 取得平均薪水最高的部门的部门名称

   > - 各部门的平均薪水
   >
   >   select d.dname, avg(e.sal) as avgsal from emp e join dept d on e.deptno=d.deptno group by d.dname order by avgsal desc limit 1;
   >
   >   +------------+-------------+
   >   | dname      | avgsal      |
   >   +------------+-------------+
   >   | ACCOUNTING | 2916.666667 |
   >   +------------+-------------+
   >   1 row in set (0.00 sec)

7. 求平均薪水的等级最低的部门的部门名称

   > - 按照部门名称分组，找出每个部门的平均薪水（薪水最低的部门则薪水等级最低）
   >
   >   select d.dname,avg(e.sal) as  avgsal from emp e join dept d on d.deptno=e.deptno group by d.dname;
   >
   >   +------------+-------------+
   >   | dname      | avgsal      |
   >   +------------+-------------+
   >   | RESEARCH   | 2175.000000 |
   >   | SALES      | 1566.666667 |
   >   | ACCOUNTING | 2916.666667 |
   >   +------------+-------------+3 rows in set (0.00 sec)
   >
   > - 找出每个部门平均薪水等级，和salgrade表连接，条件t.avgsal between s.losal and s.hisal;
   >
   >   select t.*,s.grade from (select d.dname,avg(e.sal) as  avgsal from emp e join dept d on d.deptno=e.deptno group by d.dname) t join salgrade s on t.avgsal between s.losal and s.hisal;
   >
   >   +------------+-------------+-------+
   >   | dname      | avgsal      | grade |
   >   +------------+-------------+-------+
   >   | RESEARCH   | 2175.000000 |     4 |
   >   | SALES      | 1566.666667 |     3 |
   >   | ACCOUNTING | 2916.666667 |     4 |
   >   +------------+-------------+-------+
   >   3 rows in set (0.00 sec)
   >
   > - 求最低平均薪水等级（最低平均薪水等级部门可能不止一个）
   >
   >   select min(t.grade) from (select t.*,s.grade from (select d.dname,avg(e.sal) as  avgsal from emp e join dept d on d.deptno=e.deptno group by d.dname) t join salgrade s on t.avgsal between s.losal and s.hisal) t;
   >
   >   +--------------+
   >   | min(t.grade) |
   >   +--------------+
   >   |            3 |
   >   +--------------+
   >   1 row in set (0.00 sec)
   >
   > - 求最终结果
   >
   >   select t.*,s.grade from (select d.dname,avg(e.sal) as  avgsal from emp e join dept d on d.deptno=e.deptno group by d.dname) t join salgrade s on t.avgsal between s.losal and s.hisal where s.grade=(select min(t.grade) from (select t.*,s.grade from (select d.dname,avg(e.sal) as  avgsal from emp e join dept d on d.deptno=e.deptno group by d.dname) t join salgrade s on t.avgsal between s.losal and s.hisal) t);
   >
   >   +-------+-------------+-------+
   >   | dname | avgsal      | grade |
   >   +-------+-------------+-------+
   >   | SALES | 1566.666667 |     3 |
   >   +-------+-------------+-------+
   >   1 row in set (0.00 sec)

8. 取得比普通员工(员工代码没有在mgr字段上出现的)的最高薪水还要高的领导人姓名

   > - 普通员工：不管人
   >
   >   非普通员工：管人
   >
   >   select distinct mgr from emp;
   >
   >   +------+
   >   | mgr  |
   >   +------+
   >   | 7902 |
   >   | 7698 |
   >   | 7839 |
   >   | 7566 |
   >   | NULL |
   >   | 7788 |
   >   | 7782 |
   >   +------+
   >   7 rows in set (0.00 sec)
   >
   > - 普通员工的最高薪水
   >
   >   **使用not in时记得排除null**
   >
   >   select max(sal) from emp where empno not in (select distinct mgr from emp where mgr is not null);
   >
   >   +----------+
   >   | max(sal) |
   >   +----------+
   >   |  1600.00 |
   >   +----------+
   >   1 row in set (0.00 sec)
   >
   > - 比普通员工的最高薪水还要高的领导人姓名
   >
   >   select ename,sal from emp where sal>(select max(sal) from emp where empno not in (select distinct mgr from emp where mgr is not null));
   >
   >   +-------+---------+
   >   | ename | sal     |
   >   +-------+---------+
   >   | JONES | 2975.00 |
   >   | BLAKE | 2850.00 |
   >   | CLARK | 2450.00 |
   >   | SCOTT | 3000.00 |
   >   | KING  | 5000.00 |
   >   | FORD  | 3000.00 |
   >   +-------+---------+
   >   6 rows in set (0.00 sec)

9. 取得薪水最高的前五名员工

   > select ename,sal from emp order by sal desc limit 5;
   >
   > +-------+---------+
   > | ename | sal     |
   > +-------+---------+
   > | KING  | 5000.00 |
   > | SCOTT | 3000.00 |
   > | FORD  | 3000.00 |
   > | JONES | 2975.00 |
   > | BLAKE | 2850.00 |
   > +-------+---------+
   > 5 rows in set (0.00 sec)

10. 取得薪水最高的第六到第十名员工

    > select ename,sal from emp order by sal desc limit 5,5;
    >
    > +--------+---------+
    > | ename  | sal     |
    > +--------+---------+
    > | CLARK  | 2450.00 |
    > | ALLEN  | 1600.00 |
    > | TURNER | 1500.00 |
    > | MILLER | 1300.00 |
    > | WARD   | 1250.00 |
    > +--------+---------+
    > 5 rows in set (0.00 sec)

11. 取得最后入职的5名员工

    > select ename,hiredate from emp order by hiredate desc limit 5;
    >
    > +--------+------------+
    > | ename  | hiredate   |
    > +--------+------------+
    > | ADAMS  | 1987-05-23 |
    > | SCOTT  | 1987-04-19 |
    > | MILLER | 1982-01-23 |
    > | JAMES  | 1981-12-03 |
    > | FORD   | 1981-12-03 |
    > +--------+------------+

12. 取得每个薪水等级有多少员工

    > - 每个员工的薪水等级
    >
    >   select e.ename,e.sal,s.grade from emp e join salgrade s on e.sal between s.losal and s.hisal;
    >
    >   +--------+---------+-------+
    >   | ename  | sal     | grade |
    >   +--------+---------+-------+
    >   | SMITH  |  800.00 |     1 |
    >   | ALLEN  | 1600.00 |     3 |
    >   | WARD   | 1250.00 |     2 |
    >   | JONES  | 2975.00 |     4 |
    >   | MARTIN | 1250.00 |     2 |
    >   | BLAKE  | 2850.00 |     4 |
    >   | CLARK  | 2450.00 |     4 |
    >   | SCOTT  | 3000.00 |     4 |
    >   | KING   | 5000.00 |     5 |
    >   | TURNER | 1500.00 |     3 |
    >   | ADAMS  | 1100.00 |     1 |
    >   | JAMES  |  950.00 |     1 |
    >   | FORD   | 3000.00 |     4 |
    >   | MILLER | 1300.00 |     2 |
    >   +--------+---------+-------+
    >   14 rows in set (0.00 sec)
    >
    > - 统计每个等级有多少员工
    >
    >   select s.grade,count(*) from emp e join salgrade s on e.sal between s.losal and s.hisal group by s.grade;
    >
    >   +-------+----------+
    >   | grade | count(*) |
    >   +-------+----------+
    >   |     1 |        3 |
    >   |     3 |        2 |
    >   |     2 |        3 |
    >   |     4 |        5 |
    >   |     5 |        1 |
    >   +-------+----------+
    >   5 rows in set (0.00 sec)

13. 面试题
    有3个表S(学生表)，C（课程表），SC（学生选课表）
    S（SNO，SNAME）代表（学号，姓名）  
    C（CNO，CNAME，CTEACHER）代表（课号，课名，教师）
    SC（SNO，CNO，SCGRADE）代表（学号，课号，成绩）
    问题：
    1，找出没选过“黎明”老师的所有学生姓名。
    2，列出2门以上（含2门）不及格学生姓名及平均成绩。
    3，即学过1号课程又学过2号课所有学生的姓名。

CREATE TABLE SC
(
  SNO      VARCHAR(200),
  CNO      VARCHAR(200),
  SCGRADE  VARCHAR(200)
);

CREATE TABLE S
(
  SNO    VARCHAR(200 ),
  SNAME  VARCHAR(200)
);

CREATE TABLE C
(
  CNO       VARCHAR(200),
  CNAME     VARCHAR(200),
  CTEACHER  VARCHAR(200)
);

INSERT INTO C ( CNO, CNAME, CTEACHER ) VALUES ( '1', '语文', '张'); 
INSERT INTO C ( CNO, CNAME, CTEACHER ) VALUES ( '2', '政治', '王'); 
INSERT INTO C ( CNO, CNAME, CTEACHER ) VALUES ( '3', '英语', '李'); 
INSERT INTO C ( CNO, CNAME, CTEACHER ) VALUES ( '4', '数学', '赵'); 
INSERT INTO C ( CNO, CNAME, CTEACHER ) VALUES ( '5', '物理', '黎明'); 
commit;

INSERT INTO S ( SNO, SNAME ) VALUES ( '1', '学生1'); 
INSERT INTO S ( SNO, SNAME ) VALUES ( '2', '学生2'); 
INSERT INTO S ( SNO, SNAME ) VALUES ( '3', '学生3'); 
INSERT INTO S ( SNO, SNAME ) VALUES ( '4', '学生4'); 
commit;

INSERT INTO SC ( SNO, CNO, SCGRADE ) VALUES ( '1', '1', '40'); 
INSERT INTO SC ( SNO, CNO, SCGRADE ) VALUES ( '1', '2', '30'); 
INSERT INTO SC ( SNO, CNO, SCGRADE ) VALUES ( '1', '3', '20'); 
INSERT INTO SC ( SNO, CNO, SCGRADE ) VALUES ( '1', '4', '80'); 
INSERT INTO SC ( SNO, CNO, SCGRADE ) VALUES ( '1', '5', '60'); 
INSERT INTO SC ( SNO, CNO, SCGRADE ) VALUES ( '2', '1', '60'); 
INSERT INTO SC ( SNO, CNO, SCGRADE ) VALUES ( '2', '2', '60'); 
INSERT INTO SC ( SNO, CNO, SCGRADE ) VALUES ( '2', '3', '60'); 
INSERT INTO SC ( SNO, CNO, SCGRADE ) VALUES ( '2', '4', '60'); 
INSERT INTO SC ( SNO, CNO, SCGRADE ) VALUES ( '2', '5', '40'); 
INSERT INTO SC ( SNO, CNO, SCGRADE ) VALUES ( '3', '1', '60'); 
INSERT INTO SC ( SNO, CNO, SCGRADE ) VALUES ( '3', '3', '80'); 
commit;

问题1.找出没选过“黎明”老师的所有学生姓名。
即:

 

问题2:列出2门以上（含2门）不及格学生姓名及平均成绩。


问题3:即学过1号课程又学过2号课所有学生的姓名。



14. 列出所有员工及领导的姓名

    > select a.ename '员工',b.ename '领导' from emp a **left** join emp b on a.mgr=b.empno;
    >
    > +--------+-------+
    > | 员工   | 领导  |
    > +--------+-------+
    > | SMITH  | FORD  |
    > | ALLEN  | BLAKE |
    > | WARD   | BLAKE |
    > | JONES  | KING  |
    > | MARTIN | BLAKE |
    > | BLAKE  | KING  |
    > | CLARK  | KING  |
    > | SCOTT  | JONES |
    > | KING   | NULL  |
    > | TURNER | BLAKE |
    > | ADAMS  | SCOTT |
    > | JAMES  | BLAKE |
    > | FORD   | JONES |
    > | MILLER | CLARK |
    > +--------+-------+
    > 14 rows in set (0.00 sec)

15. 列出受雇日期早于其直接上级的所有员工的编号,姓名,部门名称

    > select a.empno,a.deptno,a.ename '员工',a.hiredate,b.ename '领导',b.hiredate,d.dname from emp a join emp b on a.mgr=b.empno and a.hiredate<b.hiredate join dept d on a.deptno=d.deptno;
    >
    > +-------+--------+-------+------------+-------+------------+------------+
    > | empno | deptno | 员工  | hiredate   | 领导  | hiredate   | dname      |
    > +-------+--------+-------+------------+-------+------------+------------+
    > |  7369 |     20 | SMITH | 1980-12-17 | FORD  | 1981-12-03 | RESEARCH   |
    > |  7499 |     30 | ALLEN | 1981-02-20 | BLAKE | 1981-05-01 | SALES      |
    > |  7521 |     30 | WARD  | 1981-02-22 | BLAKE | 1981-05-01 | SALES      |
    > |  7566 |     20 | JONES | 1981-04-02 | KING  | 1981-11-17 | RESEARCH   |
    > |  7698 |     30 | BLAKE | 1981-05-01 | KING  | 1981-11-17 | SALES      |
    > |  7782 |     10 | CLARK | 1981-06-09 | KING  | 1981-11-17 | ACCOUNTING |
    > +-------+--------+-------+------------+-------+------------+-----------

16. 列出部门名称和这些部门的员工信息,同时列出那些没有员工的部门.

    > select e.*,d.dname from emp e right join dept d  on e.deptno=d.deptno;

17. 列出至少有5个员工的所有部门

    > select d.dname from emp e join dept d on e.deptno=d.deptno **group by** e.**deptno** **having** count(*****)>=5;
    >
    > +----------+
    > | dname    |
    > +----------+
    > | RESEARCH |
    > | SALES    |
    > +----------+
    > 2 rows in set (0.00 sec)

18. 列出薪金比"SMITH"多的所有员工信息.

    > select sal from emp where ename='SMITH';
    >
    > +--------+
    > | sal    |
    > +--------+
    > | 800.00 |
    > +--------+
    > 1 row in set (0.00 sec)
    >
    > select ename,sal from emp where sal>(select sal from emp where ename='SMITH');
    >
    > +--------+---------+
    > | ename  | sal     |
    > +--------+---------+
    > | ALLEN  | 1600.00 |
    > | WARD   | 1250.00 |
    > | JONES  | 2975.00 |
    > | MARTIN | 1250.00 |
    > | BLAKE  | 2850.00 |
    > | CLARK  | 2450.00 |
    > | SCOTT  | 3000.00 |
    > | KING   | 5000.00 |
    > | TURNER | 1500.00 |
    > | ADAMS  | 1100.00 |
    > | JAMES  |  950.00 |
    > | FORD   | 3000.00 |
    > | MILLER | 1300.00 |
    > +--------+---------+
    > 13 rows in set (0.00 sec)

19. 列出所有"CLERK"(办事员)的姓名及其部门名称,部门的人数.

    > - 所有"CLERK"(办事员)的姓名及其部门名称
    >
    >   select e.ename,e.job,e.deptno,d.dname from emp e join dept d on e.deptno=d.deptno where e.job='CLERK';
    >
    >   +--------+-------+--------+------------+
    >   | ename  | job   | deptno | dname      |
    >   +--------+-------+--------+------------+
    >   | SMITH  | CLERK |     20 | RESEARCH   |
    >   | ADAMS  | CLERK |     20 | RESEARCH   |
    >   | JAMES  | CLERK |     30 | SALES      |
    >   | MILLER | CLERK |     10 | ACCOUNTING |
    >   +--------+-------+--------+------------+
    >   4 rows in set (0.00 sec)
    >
    > - select deptno, count(*) as countnum from emp group by deptno;
    >
    >   +--------+----------+
    >   | deptno | countnum |
    >   +--------+----------+
    >   |     20 |        5 |
    >   |     30 |        6 |
    >   |     10 |        3 |
    >   +--------+----------+
    >   3 rows in set (0.00 sec)
    >
    > - 将第二步得到的表作为t，与第一步连接
    >
    >   select e.ename,e.job,t.countnum,d.dname from emp e join dept d on e.deptno=d.deptno  join (select deptno, count(*) as countnum from emp group by deptno) t on e.deptno=t.deptno where e.job='CLERK';
    >
    >   +--------+-------+----------+------------+
    >   | ename  | job   | countnum | dname      |
    >   +--------+-------+----------+------------+
    >   | SMITH  | CLERK |        5 | RESEARCH   |
    >   | ADAMS  | CLERK |        5 | RESEARCH   |
    >   | JAMES  | CLERK |        6 | SALES      |
    >   | MILLER | CLERK |        3 | ACCOUNTING |
    >   +--------+-------+----------+------------+

20. 列出最低薪金大于1500的各种工作及从事此工作的全部雇员人数.

    > - select job,count(*) from emp group by job having min(sal)>1500;
    >
    > +-----------+----------+
    > | job       | count(*) |
    > +-----------+----------+
    > | MANAGER   |        3 |
    > | ANALYST   |        2 |
    > | PRESIDENT |        1 |
    > +-----------+----------+
    > 3 rows in set (0.00 sec)

21. 列出在部门"SALES"<销售部>工作的员工的姓名,假定不知道销售部的部门编号.

    > - select deptno from dept where dname='SALES';
    >
    >   +--------+
    >   | deptno |
    >   +--------+
    >   |     30 |
    >   +--------+
    >   1 row in set (0.00 sec)
    >
    > - select ename from emp where deptno=(select deptno from dept where dname='SALES');
    >
    >   +--------+
    >   | ename  |
    >   +--------+
    >   | ALLEN  |
    >   | WARD   |
    >   | MARTIN |
    >   | BLAKE  |
    >   | TURNER |
    >   | JAMES  |
    >   +--------+
    >   6 rows in set (0.00 sec)

22. 列出薪金高于公司平均薪金的所有员工,所在部门,上级领导,雇员的工资等级.

    > - select avg(sal) from emp;
    >
    > - select e1.ename '员工',d.dname,e2.ename '领导',s.grade from emp e1 join dept d on e1.deptno=d.deptno left join emp e2 on e1.mgr=e2.empno join salgrade s on e1.sal between s.losal and s.hisal where e1.sal>(select avg(sal) from emp);
    >
    >   +-------+------------+-------+-------+
    >   | 员工  | dname      | 领导  | grade |
    >   +-------+------------+-------+-------+
    >   | JONES | RESEARCH   | KING  |     4 |
    >   | BLAKE | SALES      | KING  |     4 |
    >   | CLARK | ACCOUNTING | KING  |     4 |
    >   | SCOTT | RESEARCH   | JONES |     4 |
    >   | FORD  | RESEARCH   | JONES |     4 |
    >   | KING  | ACCOUNTING | NULL  |     5 |
    >   +-------+------------+-------+-------+
    >   6 rows in set (0.00 sec)

23. 列出与"SCOTT"从事相同工作的所有员工及部门名称.

    > - select job from emp where ename='SCOTT';
    >
    >   +---------+
    >   | job     |
    >   +---------+
    >   | ANALYST |
    >   +---------+
    >   1 row in set (0.00 sec)
    >
    > - select e.ename,e.job,d.dname from emp e join dept d on e.deptno=d.deptno where e.job=(select job from emp where ename='SCOTT') and e.ename<>'SCOTT';
    >
    >   +-------+---------+----------+
    >   | ename | job     | dname    |
    >   +-------+---------+----------+
    >   | FORD  | ANALYST | RESEARCH |
    >   +-------+---------+----------+
    >   1 row in set (0.00 sec)

24. 列出薪金等于部门30中员工的薪金的其他员工的姓名和薪金.

    > - select distinct sal from emp where deptno='30';
    >
    >   +---------+
    >   | sal     |
    >   +---------+
    >   | 1600.00 |
    >   | 1250.00 |
    >   | 2850.00 |
    >   | 1500.00 |
    >   |  950.00 |
    >   +---------+
    >   5 rows in set (0.00 sec)
    >
    > - select ename,sal from emp where sal in (select distinct sal from emp where deptno='30') and deptno<>'30';
    >
    >   Empty set (0.00 sec)

25. 列出薪金高于在部门30工作的所有员工的薪金的员工姓名和薪金.部门名称.

    > - select max(sal) from emp where deptno='30';
    >
    >   +----------+
    >   | max(sal) |
    >   +----------+
    >   |  2850.00 |
    >   +----------+
    >
    > - select e.ename,e.sal,d.dname from emp e join dept d on e.deptno=d.deptno where e.sal>(select max(sal) from emp where deptno='30');
    >
    >   +-------+---------+------------+
    >   | ename | sal     | dname      |
    >   +-------+---------+------------+
    >   | JONES | 2975.00 | RESEARCH   |
    >   | SCOTT | 3000.00 | RESEARCH   |
    >   | KING  | 5000.00 | ACCOUNTING |
    >   | FORD  | 3000.00 | RESEARCH   |
    >   +-------+---------+------------+
    >   4 rows in set (0.00 sec)

26. 列出在每个部门工作的员工数量,平均工资和平均服务期限.

    count函数自动忽略空

    > - select d.*,count(e.ename) from emp e right join dept d on e.deptno=d.deptno group by d.deptno;
    >
    >   +--------+------------+----------+----------------+
    >   | DEPTNO | DNAME      | LOC      | count(e.ename) |
    >   +--------+------------+----------+----------------+
    >   |     10 | ACCOUNTING | NEW YORK |              3 |
    >   |     20 | RESEARCH   | DALLAS   |              5 |
    >   |     30 | SALES      | CHICAGO  |              6 |
    >   |     40 | OPERATIONS | BOSTON   |              0 |
    >   +--------+------------+----------+----------------+
    >   4 rows in set (0.00 sec)
    >
    > - select d.*,count(e.ename),ifnull(avg(e.sal) ,0) from emp e right join dept d on e.deptno=d.deptno group by d.deptno;
    >
    >   +--------+------------+----------+----------------+-----------------------+
    >   | DEPTNO | DNAME      | LOC      | count(e.ename) | ifnull(avg(e.sal) ,0) |
    >   +--------+------------+----------+----------------+-----------------------+
    >   |     10 | ACCOUNTING | NEW YORK |              3 |           2916.666667 |
    >   |     20 | RESEARCH   | DALLAS   |              5 |           2175.000000 |
    >   |     30 | SALES      | CHICAGO  |              6 |           1566.666667 |
    >   |     40 | OPERATIONS | BOSTON   |              0 |              0.000000 |
    >   +--------+------------+----------+----------------+-----------------------+
    >
    > - select d.*,count(e.ename),ifnull(avg(e.sal) ,0),ifnull(avg(TimeStampDiff(YEAR,e.hiredate,now())),0) from emp e right join dept d on e.deptno=d.deptno group by d.deptno;
    >
    >   **在mysql中计算日期差：TimeStampDiff(间隔类型,前一个日期,后一个日期)**
    >
    >   +--------+------------+----------+----------------+-----------------------+-----------------------------------------------------+
    >   | DEPTNO | DNAME      | LOC      | count(e.ename) | ifnull(avg(e.sal) ,0) | ifnull(avg(TimeStampDiff(YEAR,e.hiredate,now())),0) |
    >   +--------+------------+----------+----------------+-----------------------+-----------------------------------------------------+
    >   |     10 | ACCOUNTING | NEW YORK |              3 |           2916.666667 |                                             38.6667 |
    >   |     20 | RESEARCH   | DALLAS   |              5 |           2175.000000 |                                             36.8000 |
    >   |     30 | SALES      | CHICAGO  |              6 |           1566.666667 |                                             39.0000 |
    >   |     40 | OPERATIONS | BOSTON   |              0 |              0.000000 |                                              0.0000 |
    >   +--------+------------+----------+----------------+-----------------------+-----------------------------------------------------+
    >   4 rows in set (0.01 sec)

27. 列出所有员工的姓名、部门名称和工资。

    > select e.ename,d.dname,e.sal from emp e join dept d on e.deptno=d.deptno;

28. 列出所有部门的详细信息和人数

    > select d.*,count(e.ename) from emp e right join dept d on e.deptno=d.deptno group by d.deptno;
    >
    > +--------+------------+----------+----------------+
    > | DEPTNO | DNAME      | LOC      | count(e.ename) |
    > +--------+------------+----------+----------------+
    > |     10 | ACCOUNTING | NEW YORK |              3 |
    > |     20 | RESEARCH   | DALLAS   |              5 |
    > |     30 | SALES      | CHICAGO  |              6 |
    > |     40 | OPERATIONS | BOSTON   |              0 |
    > +--------+------------+----------+----------------+
    > 4 rows in set (0.00 sec)

29. 列出各种工作的最低工资及从事此工作的雇员姓名

    > - select job,min(sal) as minsal from emp group by job;
    >
    >   +-----------+---------+
    >   | job       | minsal  |
    >   +-----------+---------+
    >   | CLERK     |  800.00 |
    >   | SALESMAN  | 1250.00 |
    >   | MANAGER   | 2450.00 |
    >   | ANALYST   | 3000.00 |
    >   | PRESIDENT | 5000.00 |
    >   +-----------+---------+
    >   5 rows in set (0.00 sec)
    >
    >   **由于当一条语句中有group by时，select后只能跟分组函数和参与分组的字段，故不能直接加ename**
    >
    > - 自连接
    >
    >   select e.ename,t.* from emp e join (select job,min(sal) as minsal from emp group by job) t on e.job=t.job and e.sal=t.minsal;
    >
    >   +--------+-----------+---------+
    >   | ename  | job       | minsal  |
    >   +--------+-----------+---------+
    >   | SMITH  | CLERK     |  800.00 |
    >   | WARD   | SALESMAN  | 1250.00 |
    >   | MARTIN | SALESMAN  | 1250.00 |
    >   | CLARK  | MANAGER   | 2450.00 |
    >   | SCOTT  | ANALYST   | 3000.00 |
    >   | KING   | PRESIDENT | 5000.00 |
    >   | FORD   | ANALYST   | 3000.00 |
    >   +--------+-----------+---------+
    >   7 rows in set (0.00 sec)

30. 列出各个部门的MANAGER(领导)的最低薪金

    > - 各个部门的MANAGER
    >
    >   select deptno,min(sal) from emp where job='MANAGER' group by deptno;
    >
    >   +--------+----------+
    >   | deptno | min(sal) |
    >   +--------+----------+
    >   |     20 |  2975.00 |
    >   |     30 |  2850.00 |
    >   |     10 |  2450.00 |
    >   +--------+----------+
    >   3 rows in set (0.00 sec)

31. 列出所有员工的年工资,按年薪从低到高排序

    > select ename,(sal+ifnull(comm,0))*12 as yearsal from emp order by yearsal asc;
    >
    > | ename  | yearsal  |
    > +--------+----------+
    > | SMITH  |  9600.00 |
    > | JAMES  | 11400.00 |
    > | ADAMS  | 13200.00 |
    > | MILLER | 15600.00 |
    > | TURNER | 18000.00 |
    > | WARD   | 21000.00 |
    > | ALLEN  | 22800.00 |
    > | CLARK  | 29400.00 |
    > | MARTIN | 31800.00 |
    > | BLAKE  | 34200.00 |
    > | JONES  | 35700.00 |
    > | SCOTT  | 36000.00 |
    > | FORD   | 36000.00 |
    > | KING   | 60000.00 |
    > +--------+----------+
    > 14 rows in set (0.00 sec)

32. 求出员工领导的薪水超过3000的员工名称与领导名称

    > select e1.ename '员工',e2.ename '领导' from emp e1 join emp e2 on e1.mgr=e2.empno where e2.sal>3000;
    >
    > +-------+------+
    > | 员工  | 领导 |
    > +-------+------+
    > | JONES | KING |
    > | BLAKE | KING |
    > | CLARK | KING |
    > +-------+------+
    > 3 rows in set (0.00 sec)

33. 求出部门名称中,带'S'字符的部门员工的工资合计、部门人数.

    > select d.deptno,d.dname,count(e.ename),ifnull(sum(e.sal),0) from emp e right join dept d on e.deptno=d.deptno where d.dname like '%s%' group by d.deptno,d.dname;
    >
    > +--------+------------+----------------+----------------------+
    > | deptno | dname      | count(e.ename) | ifnull(sum(e.sal),0) |
    > +--------+------------+----------------+----------------------+
    > |     20 | RESEARCH   |              5 |             10875.00 |
    > |     30 | SALES      |              6 |              9400.00 |
    > |     40 | OPERATIONS |              0 |                 0.00 |
    > +--------+------------+----------------+----------------------+

34. 给任职日期超过30年的员工加薪10%.

    > update emp set sal=sal*1.1 where timestampdiff(YEAR,hiredate,now())>30;

# DOWN