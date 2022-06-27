mysql

Mysql的启动

```sql
net stop mysql80
net start mysql80
```

客户端连接

```sql
mysql:127.0.0.1:3306 -uroot -proot
```

数据模型

```tex
数据库
表
```

sql注释注释

```sql
-- 单行注释
/*
多行注释
*/
```

SQL分类

```sql
DDL 数据定义语言(用来定义数据库对对象 库，表，字段)(可以用工具)
DML数据操做语言(用来对数据进行增删改(***))(ORM框架也可以动态生成)
DQL(数据库查询语言用来查询数据员库表中的记录)(select)
DCL(数据控制语言,主要用来创建数据库用户)(安装，分配权限)
```

DDL

```sql
-- 查询所有数据库
show databases;
-- 查询当前数据库
select database();
-- 创建库
-- charset utf-8
-- collate 排序规则
create database if exists database_name default charset char_name collate collate_raw;
-- 删除 
drop database if exists database_name;
-- 使用
use database_name

-- 查询当前数据库所有表
show table;
-- 查询表结构
desc table_name1;
-- 查询指定表的建表语句
show create table table_name1;

-- 创建表
create table table_name1(
字段名,数据类型(数据长度) 注释 
    name varchar(8) commit '姓名',
    ...
)commit '表注释'

-- 添加字段 [可以通过工具]
alter table table_name1 字段名 类型[长度] commit '注释' 约束

-- 修改数据类型
alter table table_name1 modify 字段名 数据类型[长度];

-- 修改字段名和字段类型
alter table table_name1 change old字段 new字段 类型[长度] commit '注解' 约束。

-- 删除字段
alter table table_name1 drop 字段名

-- 删除表
drop table if exists table_name1


-- 删除指定表,并重新创建表
truncate table table_name1;

```

sql常用数据类型

```sql
数字
tinyint 1byte  socre tinyint unsigned 
smallint 2byte 
mediumint 3byte
int 4byte
bigint 8byte
float 4byte
double 8byte double(4,1)
decimal[重要设计者金额必须用它] decimal[m,d]
字符串
char(10) 定了的 性能好 sex
varchar(10) 没有定的 性能相对差
日期类型
date yyyy-MM-dd birthday 3
time HH:MM:SS 3
year yyyy 1
datetime(8) YYYY-MM-dd HH:MM:ss (日期时间的混合)
timestamp(4) YYYY-MM-dd HH:MM:SS(混合时间日期，时间戳)

```





数据库操操工具类

```text
datagrip 可以直接再idea上使用
```





DML

```sql
-- 添加数据
insert into table_name1(..) values(..)
insert into table_name1 values(...)
insert into table_name1(..)values(..),(..),(..)
insert into table_name1 values(...),(...),(...)
-- 修改数据
update table_name1 set 字段名1=值,字段名2=值,...where(条件)[没有条件就是更新表中所有的行]
-- 删除数据
delete from table_name1 [where 条件] [没有条件就是删除表中所有数据]

```





DQL

```sql
select 
	字段列表[*表示所有字段] 
from 
	表名的列表
where 
	条件列表
group by 
	分组字段
having 
	分组后的条件列表
order by
	排序字段的列表
limit
	分页参数


1.基础查询1
select 字段1 as 别名1 ... from table_name1;
as[别名][可省略]
  基础查询2
select distinct 字段列表 from table_name1;
distinct 去重



2.条件查询[字段列表用*表示 不过在实际开发过程别这样写]
select * from table_name1 where 条件列表
条件
> >= < <= = 
<>!=  不等于 
between .. and .. 在什么之间
in(..)   在列表中
like 占位符 模糊匹配(_匹配一个字符，%匹配任意许多的字符)。
is null 是空
and 或者 && 并且 多条件同时成立
or 或 || 或者 任意条件成立
not 或者 ! 否，不是


3.聚合函数(统计某一列数据的函数)
count(*)统计数量
max()最大值
min()最小值
avg()平均值
sum()求和

select count(*) from table_name1;

4.分组查询
select * from table_name1 [where] group by[按什么字段进行分组 性别 年龄 年级[] 字段名 having[分组之后过滤条件] 
where筛选在分组之前 having在分组之后
                  
5.排序查询
  select * from table_name1 order by 字段1 排序方式1,字段2，排序方式2[asc升序 desc降序]

6.分页查询
 select * from table_name1 limit 0,1000

7.查询案列
1.查询年龄为20，21，22，23(in [where or[不推荐]])
2.查询性别为男，并且年龄在20-40岁内[含] 名字为3个个字的员工
3.统计员工表中年龄小于60岁的，男性员工和女性员工的人数
4.查询所有年前小于等于35岁的员工的姓名和年龄，并且对查询结国按年龄进行升序排序，如果年龄相同就安入职时间进行升序排序
5.查询性别为男，且年龄在20-40岁以内的前5员工信息，对查询结果按升序排序，年龄相同按入职时间升序排序。
                                           
8.执行顺序 from where group by having select order by limit
```



DCL

```sql
-- 查询用户
use mysql;
select * from user;
-- 创建用户
create user '用户名@主机名' IDENTIFIED by '密码';
-- 修改用户密码
alter user '用户名@主机名' IDENTIFIED  with mysql_native _passwprdprord by 'new_password'
-- 删除用户
drop user '用户名@主机名';
-- 查询权限
show grants from '用户名@主机名'
-- 授权
grant 权限列表 on 数据库.表名 to '用户名@主机名'
-- 撤销权限
revoke 权限列表 on 数据库.表名 from '用户名@主机名'




```

函数

```sql
-- 函数 是指一段可以直接在另一段程序中调用的程序或者代码
-- 字符串函数
concat(s1,s2)拼接
lower(str) 将大写转换为小写
upper(Str)将小写转换为大写
lpad(str,n,pad) 左填充
rpad(str,n,pad) 右填充
trim(str) 去掉头部和尾部的空格
substring(str,start len) 返回一个从字符串从start位置起的一个
-- 数值函数
len长度的字符串
ceil(x)向上取整，
floor(X)向下取整，
mod(x，y)返回x/y的模
rand()返回0-1内的整数
round()求参数x的四舍五入

-- 日期函数
curdate()  返回当前日期
curtime()  返回当前时间
now()      返回当前日期和时间
year(date)     返回年
month(date)	   返回月
day(date)      返回天
date_add(date,interval(expr type)) 返回一个日期/时间值上加上一个间隔时间
dateoff(date1,date2) 返回起始时间date1和结束时间date之间的天数


-- 流程函数
if(val,t,f) 如果值为ture返回t，否则f
ifnull(value1,value2) 如果value1不为空，返回value1，是就返回value2


```

约束

```sql
-- 限制字段的规则，保证数据的正确，有效性和完整性
not null -- 非空
unque -- 唯一
primary key -- 主键
default -- 默认 是未指定的情况
foreign key -- 外键
check -- 保证字段值满足一个条件

auto_increment -- 自动增长

create table user(
id int primary key auto_increment comment '主键',
    name varchar(8) not null unique comment '姓名',
    age int check(age>0&&age<120)comment '年龄',
    status char(1) default '1' comment '状态',
    gender char(1) comment '性别',
    constraint '外键名称' foreign key [外键字段名] references 主表 [主要列名]
) comment '用户表'

-- 外键约束
--  用来在两张表数据之间产生联系。
alter table 表名 add constraint 外键名称 foreign key [外键字段] references 主表 [主表列名]

-- 删除外键 
alter table 表名 drop foreign key 外键名称。

-- 级联更新，级联删除[不推荐] [阿里巴巴开发手册说过，一切与外键有关的操做都要在应用层完成]

```

多表查询

```sql
-- 多表关系 1：1 1：n n:m
-- 笛卡尔乘积
-- 隐式内连接
 select * from 表1,表2 where [条件]
-- 显示内连接
select * from 表1 [inner] join 表2 on [连接条件]
-- 左外连接 
select * from 表1 left [outer] join 表2 on [连接条件]
-- 右外连接
select * from 表1 right [outer] join 表2 on [连接条件]
-- 子链接
select * from 表1 as 别名A join 表B 别名B on [连接条件]

-- 联合查询
-- 把多次查询的结果合并起来，形成一个新的查询结果集

select 字段列表 from 表A ... union[all] select 字段列表 from 
表b ...

-- 子查询
-- SQL语句中嵌套select 语句 又称子查询。
select * from t1 where column1=[select column1 form t2]


-- 标准子查询
-- 子查询的结果返回的是单个值
-- 常用的操做符 > <> > >= <=

-- 列子查询
-- 子查询放回
-- 常用的操做符: IN NOT IN ANY SOME ALL

-- 行子查询 
-- 子查询返回结果是一行(最多是多列)
-- 常见的操做符： = <> IN NOT IN

-- 表子查询
-- 子查询差出来的是多行多列
-- 常用操做符[in]


-- 多表查询联系
1.查询员工姓名，
```



事务

```sql
-- 事务的定义
-- 一组操做，同时成功，同时失败
-- 事务的特性
-- 原子性: 事务即是最小的操做单位，不可分割
-- 一致性: 事务完成数据总量保持一致
-- 隔离性：并发的时候，事务之间不能相互影响、
-- 持久性: 事务完成后，对数据的操做是持久的。

-- 并发事务的问题 
-- 脏读  一个事务读到其他事务没有提交的数据
-- 不可重复读 一个事务先后读到同一条记录。但是两次读到的数据不同
-- 幻读 一个事务在按条件查询的时候没有对应的记录行，但是在插入的时候又发现这行数据已经存在了。

-- 隔离级别
read-uncommited
read-commited
repeatable-read(默认)
serilalizable
```

