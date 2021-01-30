# 基本概念

关系型数据库(SQL)

* MySQL,Oracle,Sql Server,通过表和表直接,行和行之间的关系进行数据的存储

非关系型数据库(NoSQL)

* Redis,MongDB,存储对象,通过对象的自身属性来决定

DBMS数据库管理系统

* 科学有效的管理我们的数据

# 基本命令

[其它基本命令](../../基础工具/mysql.md)

```sql
--安装与登录
sudo apt-get install mysql-server -- 安装最新版MySQL服务器
sudo apt-get install libmysqlclient-dev -- 安装开发包
sudo cat /etc/mysql/debian.cnf 	--查看初始用户名和密码 
mysql -u user -ppasseword	--登录
update mysql.user set authentication_string=password('123456') where user='root' and host='localhost'; --修改当前用户密码
update mysql.user set plugin="mysql_native_password";
flush privileges;	--刷新权限
exit;	--退出
mysql -u root -p	--登录root
--用户权限管理
CREATE USER 用户名 IDENTIFIED BY '123456'	--创建用户
SET PASSWORD=PASSWORD('123456');	--修改当前用户密码
SET PASSWORD FOR 用户名=PASSWORD('123456');--修改指定用户密码
RENAME USER 用户名 TO 新用户名;	--用户重命名
GRANT ALL PRIVILEGES ON *.* TO 用户;--用户授权(全部库的全部表)
SHOW GRANTS FOR 用户名;	--查看指定用户权限
SHOW GRANTS FOR root@localhost; --查看root权限
REVOKE ALL PRIVILEGES ON *.* FROM 用户; --撤销权限
DROP USER 用户;	--删除用户
--查看密码策略
show variavles like 'validate_password%';
set global validate_password.policy=0; --修改密码策略
set global validate_password.length=4; --修改密码长度
--设置远程访问(创建一个用户负责远程访问)
create user 'kai'@'%' identified by 'password';--% 表示任何ip地址
grant all on *.* to 'kai'@'%';--授权,所有表
```

```bash
#MySQL备份
#mysqldump -h 主机 -u 用户 -p 密码 数据库 表名 > 位置
mysqldump -hlocalhost -uroot -p123456 库名 表名 > 位置
```

```bash
#win10 mysql_8

#启动服务
	#计算机管理 服务->MySQL
#初始化密码
mysqld --initialize --user=mysql --console

#修改密码
alter user 'root'@'localhost' identified with mysql_native_password by '123456';
flush privileges;
```

# 事务

## 概念

事务就是要保证一组数据库操作要么全部成功,要么全部失败

`MySQL`中事务支持是引擎层实现

事务的标准特征ACID:

原子性(Atomicity):一个事务必须被视为一个不可分割的最小工作单元,整个事务中的所有操作要么全部提交成功,要么全部失败回滚

一致性(Consistency):数据库总是从一个一致性的状态转换到另一个一致性的状态,事务前后的数据完整性要保持一致

隔离性(Isolation):一个事务所做的修改在最终提交以前,对其他事务是不可见的

持久性(Durability):一旦事务提交,则其所做的修改就会永久保存到数据库中,即使系统崩溃,修改的数据也不会丢失

## 隔离级别

隔离导致的一些问题:

* 脏读:指一个事务读取了另一个事务未提交的数据
* 不可重复读:在一个事务内读取表中的一行数据,多次读取结果不同
* 虚读(幻读):一个事务内读取到别的事务插入的数据,导致前后读取不一致

`SQL`标准事务隔离级别包括:

* 读未提交(`read uncommitted`):一个事务还没提交时,它做的变更就能被别的事务看到
* 读提交(`read committed`):一个事务提交之后,它做的变更才能被其它事务看到
* 可重复读(`repeatable read`):一个事务执行过程中看到的数据,总是跟这个事务在启动时看到数据是一致的,未提交变更对其它事务也是不可见的
* 串行化(`serializable`):对一行记录,写会加写锁,读会加读锁,当出现读写锁冲突时,后访问的事务必须等前一个事务执行完成,才能继续执行

`show variables like 'transaction_isolation'`查看隔离级别

在实现上,数据库里面会创建一个视图,访问的时候以视图的逻辑结果为准

在"可重复读"的隔离级别下,这个视图是在事务启动时创建的,整个事务存在期间都会用这个视图

在"读提交"的隔离级别下,这个视图是在每个`SQL`语句开始执行的时候创建的

"读未提交"隔离级别下直接返回记录上的最新值,没有视图概念

"串行化"隔离级别直接用加锁的方式来避免并行访问

## 隔离实现

"可重复读"说明:

在`MySQL`中,实际上每条记录在更新的时候都会同时记录一条回滚操作,记录上的最新值,通过回滚操作,都可以得到前一个状态的值

只有当没有事务再需要用这些回滚日志时,回滚日志才会被删除

长事务意味着系统里面存放了很老的事务视图,由于这些事务随时可能访问数据库里面的任何数据,所以这个事务提交之前,数据库里面它可能用到的回滚记录必须保留,这会导致大量占用存储空间,此外,长事务还占用锁资源,也可能拖垮整个库,所以尽量不要使用长事务

## 启动方式

`MySQL`的事务启动有以下几种:

* 显示启动事务语句,`begin`或`start transaction`,提交语句`commit`,回滚语句`rollback`
* `set autocommit=0`,这个命令会将这个线程的自动提交关闭,如果只执行一条select语句,这个事务就启动了,而且并不会自动提交.这个事务持续存在直到主动执行commit或rollback语句,或者断开连接

有些客户端连接框架会默认连接成功后执行一个`set autocommit=0`的命令,这会导致接下来的查询都会再事务中,如果是长连接,就导致了意外的长事务

使用要使用`set autocommit=1`,通过显示语句的方式来启动事务

再`autocommit=1`的情况下,用begin显示启动事务,如果执行commit则提交事务,如果执行`commit work and chain`,则会提交事务并自动启动下一个事务,这样就省去了再次执行begin语句的开销

在`information_schema`库的`innodb_trx`这个表中查询长事务

```sql
--查找持续时间超过60s的事务
select * from information_schema.innodb_trx where TIME_TO_SEC(timediff(now(),trx_started))>60
```

# 索引

## 概念

索引是帮助MySQL高校获取数据的数据结构,提取句子主干,索引的本质是数据结构,innodb默认Btree

索引的分类:

* 主键索引 (PRIMARY KEY)
  * 主键不可重复,唯一的标识,只能有一个列作为主键
* 唯一索引 (UNIQUE KEY)
  * 避免重复的列出现,可以重复,多个列多可以标识为唯一索引
* 常规索引 (INDEX/KEY)
  * 默认的,通过key等关键字设置
* 全文索引 (FULLTEXT)
  * 快速定位数据

```sql
SHOW INDEX FROM 表名 --查看表的索引
ALTER TABLE 表名 ADD FULLTEXT INDXE 索引名(列名) --增加全文索引
CREATE INDEX 索引名 on 表(字段)	--增加常规索引
```

索引原则:

* 索引不是越多越好
* 不好对经常变动的数据加索引
* 小数据量的表不需要加索引
* 索引一般加在常用来查询的字段上

