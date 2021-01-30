service mysql start

service mysql restart

service mysql stop

mysql -u root -p

[MySQL入门笔记](../进阶提高/MySQL/MySQL入门.md)

# 库管理

创建数据库				  create database <数据库名>

显示所有的数据库			show databases （注意：最后有个s）

删除数据库				  drop database <数据库名>

连接数据库				  use <数据库名>

查看当前使用的数据库		   select database();

当前数据库包含的表信息		  show tables; 

查看数据库的字符集		    show create database 数据库名

更改数据库字符集			 alter database 数据库名 character set gbk


# 表管理

建表		create table <表名> ( <字段名> <类型> [,..<字段名n> <类型n>]);

获取表结构					desc 表名或者show columns from 表名

删除表							drop table <表名>

更改表名						alter table 原表名 rename  to 新表名;

在表中增加字段			alter table 表名 add column字段 类型 其它

删除字段						alter table 表名 drop column  字段

修改字段名称				alter table 表名 change 原名 新名 类型

修改字段类型                 alter table 表名 modify  字段 类型

# 表数据管理

插入数据				insert into <表名>  [( <字段名>)]  values  ( 值 )

查询表中的数据	select <字段，字段，...> from < 表名 > where < 表达式 >

删除表中数据		delete from 表名 where 表达式

修改表中数据 		update 表名 set 字段=新值,…where 条件

全部删除				truncate table 表名 

# 表数据查询

查询所有列					select  *  from 表名

查询指定列					select	字段,字段 ,...	from 表名

查询时添加常量列		select	字段,...,'常量列'  别名  from 表名

查询时合并列				select  字段1+字段2+字段3 as 别名 from  表名  (仅限于数字类型int)

去除重复记录				select	distinct  ....

**条件查询**						select  *  from  表名  where  条件表达式		

* 条件表达式    比较运算符   between and     like  		

* 通配符		%  匹配0或多个字符     _  匹配1个字符   like 'zhang%'      

* 逻辑   		与 AND     或  OR     非  NOT

**聚合查询**       通过包含一个聚合函数(Sum 或 Avg)来汇总来自多个行的信息

* avg()求平均值	max()求最大值	min()最小值    count()求记录数    sum()求和
* select  count(*)  from  表名              select  count(字段)  from  表名
  * count  函数会自动排除null的数据
  * 使用  *  在效率方面不如  字段(内容不为空)
* select  max(字段)   from   表名
* select   avg(字段)   from   表名

**分页查询**         limit  

* select  *   from  表名  **limit**  start,pagsize

*  select  *  from  表名  limit  (pageno-1)*pagesize,pagesize

**查询排序**       order  by  

* 降序          select  *  from   表名   **order**  **by**  字段  **desc**
* 升序          select  *  from   表名   **order**  **by**  字段  **asc**
* 多列排序    select *  from   表名   **order**  **by**   字段1  **desc**, 字段2  **asc**
  * 先按照第一排序条件进行,在第一排序相同的情况按第二排序条件进行

**分组查询**     group   by

* select   字段1,**count(*)**   from    表名   **group**   **by**  字段1
* 分组查询需要配合聚合查询
* 分组过滤   having       where只针对单行过滤
  * select  字段1,count(*)   from   表名   group  by  字段1  **having**  条件表达式

# 数据类型



# 约束

| 约束        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| NOT NULL    | 非空约束 指定某列不能空                                      |
| UNIQUE      | 唯一约束 指定某列不能重复                                    |
| PRIMARY KEY | 主键 指定该列的值可以唯一的标识该列记录                      |
| FOREIGN KEY | 外键 指针改行记录从属主表的一条记录                          |
| CHECK       | 指针一个布尔表达式,用于指定对应的值必须满足该表达式(mysql不支持check) |
| DEFAULT     | 默认值约束 指定某列的默认值                                  |

主键约束操作

```mysql
添加主键约束的方式:
	1.建立表的时候直接添加
	格式一:
	create table 表名(
		列名1 数据类型 primary key,
		列名2 数据类型
	)
	格式二:
	create table 表名(
		列名1 数据类型,
		列名2 数据类型,
		constraint pk_列名1 primary key(列名1)
	)
	格式三:
	create table 表名(
		列名1 数据类型,
		列名2 数据类型,
		primary key(列名1)
	)
	2.通过ALTER语句,已有表的情况创建主键约束
	格式一:
	alter table 表名 add constraint pk_id primary key(id)
	格式二:
	alter table 表名 add primary key(id)
	格式三:
	alter table 表名 modify 列名 数据类型 primary key
删除主键约束:
	alter table 表名 drop primary key
联合主键
	1.创建表的时候创建联合主键
	格式一:
	create table 表名(
		列名1 数据类型,
		列名2 数据类型,
		constraint pk_列名 primary key(列名1,列名2)
	)
	格式二:
	create table 表名(
		列名1 数据类型,
		列名2 数据类型,
		primary key(列名1,列名2)
	)
	
	2.对已有的表添加联合主键
	格式一:alter table 表名 add constraint pk_列名1_列名2 primary key(列名1,列名2)
	格式二:alter table 表名 add primary key(列名1,列名2)
```

唯一约束操作

```mysql
1.一旦某个列被添加了唯一约束,该列的值不允许重复,可以为空
2.一个表中可以有多个唯一约束,当该列被添加了唯一约束,数据库管理系统会给他指定一个唯一的索引
3.索引:类似于目录
4.如果不给唯一约束起名字,那么唯一约束的名字和列名一样
5.创建唯一约束:一个表中可以有多个唯一约束
	1.创建表的时候创建唯一约束
	格式一:
	create table 表名(
		列名1 数据类型 unique,
		列名2 数据类型,
		列名3 数据类型 unique
	)
	格式二:
	create table 表名(
		列名1 数据类型,
		列名2 数据类型,
		constraint 唯一约束的名字 unique(列名)
	)
	
	2.针对已有的表添加唯一约束
	alter table 表名 add unique(列名)
	
6.删除唯一约束
alter table 表名 drop index 唯一约束的名字
```

默认约束和非空约束

```mysql
1.默认值约束
	1.创建表的时候创建默认值约束:可以有多个默认值约束
	create table 表名(
	列名1 数据类型 default '值',
	列名2 数据类型 default '值',
	列名3 数据类型
	);
	2.针对有表的情况添加默认值约束
	alter table 表名 modify 列名 数据类型 default '值'
	3.删除默认值约束
	alter table 表名 modify 列名 数据类型

2.非空约束
	1.要求列的值不能为空
	2.关键字:not null
	3.一个表中可以有多个非空约束
	4.创建表时创建非空约束
	create table 表名(
		列名1 数据类型 not null,
		列名2 数据类型 not null
	)
	5.针对有表时,创建非空约束
	alter table 表名 modify 列名 数据类型 not null
	
	6.删除非空约束
	alter table 表名 modify 列名 数据类型
```

外键

```mysql
1.外键约束
	1.外键约束是实现表与表之间的参照引用
	2.如果要删除主表中有外键约束的,那么要先删除从表中依赖主表的记录
	3.有参照引用的主表的外键列必须有值
	4.注意事项：
		1.外键约束是针对两个表来说的，主表与从表，实现主表与从表之间的参照关系，依赖于在从表中针对某个列建立外键约束
		2.当主表和从表建立起外键约束时，一定是从表中的某个列参照引用主表中的某个列，相当于把一个列当作是连接两个表之间的桥梁。
		3.当主表被从表引用时，主表删除记录时，要看从表是否有引用。
		4.当向从表中做操作--插入数据，先询问主表的意见
		5.一个表中可以有多个外键约束
		6.当从表中的某个列参照主表的某个列时,要求主表的列必须是主键约束或者唯一约束
		7.当从表和主表中以某个列来建立参照约束时，要求这两个表中，列名可以不一致，但是两个列的内容要保持一致，包括数据类型
2.外键约束操作
	1.创建外键约束的关键字:foreign key  references
	格式:
	主表
	create table 表名(
		列名1 数据类型 primary key,
		列名2 数据类型 unique
	)
	从表
	create table 表名(
		列名1 数据类型,
		列名2 数据类型,
		constraint 外键约束的名字 foreign key(从表的列名1) references 主表(主表的列名1),
		constraint 外键约束的名字 foreign key(从表的列名2) references 主表(主表的列名2)
	)
	--外键约束的名字:FK_列名
	2.针对已有的表添加外键约束
	alter table 从表名 add constraint 外键约束的名字 foreign key(从表的列名) references 主表(主表的列名1);

3.删除外键约束
格式:alter table 从表名 drop foreign key 外键约束的名字
```

# 常见函数

将一组逻辑语句封装在方法体中,对外暴露方法名

好处:

* 隐藏了实现细节
* 提高了代码的重用性

调用:`select 函数名(实参列表) from 表;`

**单行函数**

```mysql
#字符函数
select length('hello');#length获取参数值的字节个数
select concat('hello','world');#concat拼接字符串
select upper('hello');#将字符串大写
select lower('HELLO');#将字符串小写
select concat(upper(last_name),lower(first_name)) name from stutable;
select substr('hello world',7);#字符截取,sql语言中索引从1开始,world
select substr('hello world'1,5);#hello
select instr('hello world','world');#instr返回字串在主串的起始索引
select trim('   world   ');#trim去字符的前后空格
select trim('a' from 'aaaworldaaa')#trim去前后a
select lpad('hello','10','*');#lpad用指定的字符实现左填充
select rpad('hello','10','*');#rpad用指定的字符实现右填充
select replace('hello world','world','mysql');#replace实现替换
```

```mysql
#数学函数
select round(3.1415);#3,round四舍五入函数
select round(3.1415,2);#3.14,保留两位小数
select ceil(3.14);#4,ceil向上取整,返回大于等于该参数的最小整数
select floor(3.14);#3,floor向下取整,返回小于等于该参数的最大整数
select truncate(3.14,1);#3.1,truncate截断,将n位后的数截断
select mod(10,3);#1,mod取余,10%3=1,a-a/b*b
```

```mysql
#日期函数
select now();#now返回当前系统日期+时间
select curdate();#curdate返回当前日期,不包含时间
select curtime();#curtime返回当前时间,不包含日期
select year(now());#year返回当前时间的年
select month(now());#month返回月,day,hour,minute,second类似
select monthname(now());#monthname返回英文
select str_to_date('9-20-1999','%m-%d-%Y');#1999-09-13,字符转换为日期
select date_format('1999/9/20','%Y %m %d');#1999 09 20,日期转换成字符
```

```mysql
#其它函数
select version();#查看版本号
select database();#当前库
select user();#当前用户
```

```mysql
#流程控制函数
select if(2>1,'yes','no');#yes,if函数类似三目运算符
#case使用1
case 要判断的字段或表达式
when 常量1 then 语句1;
when 常量2 then 语句2;
...
else 语句n;
end;#类似switch case defaul
#case使用2
case
when 条件1 then 语句1;
when 条件2 then 语句2;
...
else 语句n;
end;#类似多重if
```

**分组函数**

功能:用作统计使用,又称为聚合函数或统计函数或组函数

sum求和,avg平均值,max最大值,min最小值,count计算个数

```mysql
select sum(字段) from 表;#求和
```

参数支持哪些类型:

* sum和avg一般处理数值型
* max,min,count支持任何类型

是否忽略null:

* 以上分组函数都忽略null

可以和distinct搭配去重

# 联结表

```mysql
select 字段... from 表名1,表名2... 	#交叉查询或笛卡尔积查询(这样的查询是错误的)
select 字段... from 表名1,表名2... where 条件语句		#等值联结查询
select 字段... from 表名1 inner join 表名2 on 条件语句	#where类似
左外联结
select 字段... from 表1 left join 表2 on 条件语句		
/*左外联结,以左表为主,左表数据全部显示,右表满足联结条件则显示,不满足联结条件则显示为NULL*/
右外联结 
表1 right join 表2 on 条件语句 #以右表为主
```

```mysql
自联结
自联结需要使用表别名,表别名允许单条select语句中使用相同的表
#通过ID12345姓名张三的同学查找此班其它同学
select a._id a._name from stu a,stu b where a.class=b.class and b._id="12345";
```

# 子查询

子查询,即嵌套在其它查询中的查询

# 视图

视图是虚拟的表,行和列的数据来自定义视图的查询使用的表,并且在使用视图时动态生成,只保存了`SQL`逻辑,不保存查询结果

应用场景:

* 多个地方用到同样的查询结果
* 该查询结果使用的语句较复杂

```mysql
create view 视图名 as 语句;  			#创建视图
show create view 视图名;   			  #查看创建视图的语句
desc 视图名;						    #查看视图
drop view 视图名;	 					#删除视图
create or replace view 视图名 as 语句;	#更新视图
alter view  视图名 as 语句;	            #更新视图
```

```mysql
#实例:查询姓张的学生名和专业名
#无视图
select stuname,majorname from stuinfo s inner join major m on s.`majorid`=m.`id` where s.`stuname` like '张%';
#利用视图
create view v1 as select stuname,majorname from stuinfo s inner join major m on s.`majorid`=m.`id`;
select * from v1 where stuname like '张%';
```

好处:

* 重用sql语句
* 简化复杂的sql操作
* 保护数据,提高安全性

# 事务隔离

`SQL`标准事务隔离级别包括:

* 读未提交(`read uncommitted`):一个事务还没提交时,它做的变更就能被别的事务看到,可以出现脏读,幻读,不可重复读
* 读提交(`read committed`):一个事务提交之后,它做的变更才能被其它事务看到,可避免脏读
* 可重复读(`repeatable read`):一个事务执行过程中看到的数据,总是跟这个事务在启动时看到数据是一致的,未提交变更对其它事务也是不可见的,可避免脏读和幻读
* 串行化(`serializable`):对一行记录,写会加写锁,读会加读锁,当出现读写锁冲突时,后访问的事务必须等前一个事务执行完成,才能继续执行,可避免脏读幻读不可重复读

```mysql
select @@tx_isolation;  #查看当前的隔离级别,Mysql默认可重复读
set transaction isolation level 隔离级别  #设置当前mysql连接的隔离级别
set global transaction isolation level 隔离级别  #设置数据库系统的全局隔离级别
set autocommit=0;		#开启事务,之后编写事务中的sql语句(增删改查)
commit;					#提交事务
rollback;				#回滚事务
savepoint 节点名;			#设置节点
rollback to 节点名;		#回滚到到节点名
```

# 存储过程

存储过程是:一组预先编译好得`SQL`语句得集合(类似于C中的函数)

好处:

* 提高代码得重用性,简化操作
* 减少了编译次数并且减少了和数据库服务器得连接次数,提高了效率

```mysql
#创建语法
create procedure 存储过程名(参数列表)
begin
	存储过程体(一组合法有效的SQL语句)
end
/*注意:
1.参数列表有三部分
参数模式  参数名  参数类型
参数模式:
in		:该参数可以作为输入,需要调用方传入值
out		:该参数可以作为输出(返回值)
inout	:该参数既可以作为输入,又可以作为输出(既需要传入值,又可以返回值)
2.如果存储过程体仅仅只有一句话,begin end可以省略
3.存储过程体中的每条SQL语句结尾要求必须加分号,存储过程中的结尾可以使用DELIMITER
	语法:DELIMITER 结束标记
*/
```

```mysql
#调用语法
call 存储过程名(实参列表);
#1.空参列表
delimiter $   #$为结束标记
create procedure myp1()
begin
	insert into 表名(字段) value ('hello'),('world');
end $
call myp1()$ #调用存储过程以结束标记作结尾
#2.in模式参数
delimiter $
create procedure myp2(in _username varchar(20),in _password varchar(20))
begin
	declare result int default 0;#声明变量并初始化
	select count(*) int result   #变量赋值
	from admin
	where admin.username=_username
	and admin.password=_password;
	select if(result>0,'成功','失败');
end $
call myp2('leo','123')$
#3.out模式
create procedure myp3(in aname varchar(20),out bname varchar(20))
begin
	select 表1.name into bname
	from 表1
	inner join 表2 on 表1.id=表2.id
	where 表1.name=aname;
end $
set @_name$
call myp3('lihua',@_name)$
select @_name$
#4.inout模式
create procedure myp4(inout a int,inout b int)
begin
	set a=a*2;
	set b=b*2;
end $
set @_a=10$
set @_b=20$
call myp4(@_a,@_b)$
select @_a,@_b$
```

```mysql
#删除存储过程
drop procedure 存储过程名;#一次只能删除一个存储过程
#查看存储过程
show create procedure 存储过程名;
```

# 函数

函数与 存储过程类似

区别:

* 存储过程可以有0个返回,也可以有多个返回,适合做批量增删改
* 函数必须有且只有1个返回,适合做处理数据返回一个结果

```mysql
#创建语法
create function 函数名(参数列表) returns 返回类型
begin
	函数体
end
/*注意:
1.参数列表包含两个部分:
参数名 参数类型
2.函数体:
肯定有return语句
3.函数体中只有一句话,可以省略begin end;
4.用delimiter语句设置结束标记
*/
```

```mysql
#调用语法
select 函数名(参数列表);
#1.无参返回
#返回员工数
delimiter $
create function myf1() returns int
begin
	declare c int default 0;#定义一个局部变量
	select count(*) into c
	from employees;
	return c;
end $
select myf1()$
#2.有参有返回
#根据员工名返回他的工资
create function myf2(_name varchar(20)) returns double
begin
	set @sal=0;#定义一个用户变量
	select salary into @sal 
	from employees
	where name=_name;
	return @sal;
end $
select myf2('tom')$
```

```mysql
#查看函数
show create function 函数名;
#删除函数
drop function 函数名;
```

# 流程控制结构

顺序结构:程序从上往下依次执行

分支结构:程序从两条或多条路径中选择一条去执行

循环结构:程序在满足一定条件的基础上,重复执行一段代码

```mysql
#分支函数
1.if函数能实现简单的双分支
select if(表达式1,表达式2,表达式3);
如果表达式1成立,则if函数返回表达式2的值,否则返回表达式3的值
2.case结构
#case使用1
case 要判断的字段或表达式
when 常量1 then 语句1;
when 常量2 then 语句2;
...
else 语句n;
end case;#类似switch case defaul
#case使用2
case
when 条件1 then 语句1;
when 条件2 then 语句2;
...
else 语句n;
end case;#类似多重
3.if结构实现多重分支
if 条件1 then 语句1;
elseif 条件2 then 语句2;
...
else 语句n;
end if;#只能用在begin end中(函数和存储过程中)
```

```mysql
#循环结构
分类: while loop repeat
循环控制:
iterate 类似于continue 结束本次循环,继续下一次
leave 类似于break 结束当前所在的循环
循环控制必须使用标签
1.while
[标签:]while 循环条件 do
	循环体;
end while [标签];#类似while
2.loop
[标签:]loop
	循环体;
end loop [标签];#死循环
3.repeat
[标签:]repeat
	循环体;
until 结束循环的条件
end repeat [标签];#类似do while
```

```mysql
#1.批量插入,根据次数插入admin表中多条记录
delimiter $
create procedure pro_while(in insertcount int)
begin
	declare i int default 1;
	while i<=insertcount do
		insert into admin(username,`password`)
		values(concat('tom',i),'123');
		set i=i+1;
	end while;
end $
call pro_while(10)$
#2.如果次数大于5,则停止
drop procedure pro_while$	#先删除再创建
create procedure pro_while(in insertcount int)
begin
	declare i int default 1;
	a:while i<=insertcount do
		insert into admin(username,`password`)
		values(concat('jack',i),'234');
		if i>=5 then leave a;
		end if;
		set i=i+1;
	end while a;
end $
call pro_while(10);
```

