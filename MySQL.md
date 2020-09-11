# MySQL

### 数据库的几个数值类型

![image-20200824161729948](MySQL.assets\image-20200824161729948.png)

![image-20200824161734395](MySQL.assets\image-20200824161734395.png)

![image-20200824161739620](MySQL.assets\image-20200824161739620.png)



### 数据库的三大设计范式

\1.    第一范式

1NF

数据表中所有字段都是不可分割的原子值。

范式设计越详细，对某些实际操作可能更好，但不一定都是好处。

如设计地址时，将它拆分成省份、城市、详细地址。

则每次都需要提取三个属性拼接成一个。

 

 

\2.    第二范式

必须满足第一范式的前提下，第二范式要求：除主键外每一列都完全依赖于主键。

如果出现不完全依赖，只可能发生在联合主键的情况下。

如：

create table myorder (

product_id int,

customer_id int,

product_name varchar(20),

customer_name varchar(20),

primary key(product_id,customer_id)

);

这其中，产品名字只依赖于产品id，用户名字只依赖于用户id，不符合第二范式。

将其拆分成三个表：

Create table myorder(

Order_id int primary key,

Product_id int,

Customer_id int

);

Create table product(

Id int primary key,

Name varchar(20)

);

Create table customer(

Id int primary key,

Name varchar(20)

);

分成三个表后，满足第二范式。

 

\3.    第三范式

3NF

必须先满足第二范式，除开主键列的其他列间不能有传递依赖关系

Create table myorder(

Order_id int primary key,

Product_id int,

Customer_id int,

Customer_phone varchar(15)

);

Customer_phone和customer_id互相依赖，因此不满足第三范式。

 

### 增删查改

首先进入数据库后

输入 **show databases;**

可以看到有多少数据库。

然后选择一个数据库，如 pet

**use pet;**

就进入了这个数据库中。

**show tables;**

创建一个新表

create table student(

stnum varchar(20) **primary key**;

stname varchar(20) not null;

cnum varchar(20) not null;

**Foreign key**(cnum) **references** course(cnum)

)

查询所有记录：

select * from student;

查询某一条：

select stnum from student;

**Distinct** 是排除重复的记录。

Selcet distinct cnum from student;

**增：**

**insert** into student values('1001','李华','9-105');

改：

**update** student set  stname='王五' where stnum='1001';

删：

删除表：

drop table student;

删除某个约束：

alter table student drop primary key;

删除某个属性（列）：

alter table student drop stname;

删除某一行：

delete from student where stnum='1001';



### 四种连接查询

内连接

Inner join或者join

 

##### 外连接

\1.   左连接 left join或者left outer join

\2.   右连接 right join或right outer join

\3.   完全外连接 full join或者full outer join

 

 

创建两个表：

Person表

Id,

Name,

cardID,

crea

 

create table person (

id varchar(20) primary key,

name varchar(30) not null,

cardID varchar(20),

foreign key(cardID)references card(id)

);

 

card表

id,

name

 

create table card (

id varchar(20) primary key,

name varchar(30) not null

);

 

添加数据：

```
INSERT INTO card VALUES (1, '饭卡'), (2, '建行卡'), (3, '农行卡'), (4, '工商卡'), (5, '邮政卡');
SELECT * FROM card;
INSERT INTO person VALUES (1, '张三', 1), (2, '李四', 3), (3, '王五', 6);
SELECT * FROM person;
```

 

没有创建外键连接

 

\1.   Inner join 查询（内连接）

Select * from person inner join card on person.cardID=card.id; //内连接查询

内联查询，其实就是两张表中的数据，通过某个字段相等（大于小于），查询出相关记录数据。

 

 

##### Left join (左外连接)

Select * from person left join card on person.cardID=card.id;

+----+------+--------+------+--------+

| id | name | cardID | id  | name  |

+----+------+--------+------+--------+

| 1 | 张三 | 1  | 1  | 饭卡 |

| 2 | 李四 | 3  | 3  | 农行卡|

| 3 | 王五 | 6  | NULL | NULL  |

+----+------+--------+------+--------+

 

左外连接，会吧左边表里的所有数据取出来，而右边表中的数据，如果有相等的，就显示出来，没有相等的，会补NULL

Select * from person left outer join card on person.cardID=card.id;

 

 

##### Right join(右外连接)

Select * from person right join card on person.cardID=card.id;

+------+------+--------+----+--------+

| id  | name | cardID | id | name  |

+------+------+--------+----+--------+

| 1  | 张三 | 1   | 1 | 饭卡 |

| NULL | NULL | NULL | 2 | 建行卡|

| 2  | 李四 | 3   | 3 | 农行卡|

| NULL | NULL | NULL | 4 | 工商卡|

| NULL | NULL | NULL | 5 | 邮政卡|

+------+------+--------+----+--------+

 

右外连接，会吧右边表里所有数据取出来，而左边表的数据，如果有相等的，就显示出来，如果没有，就补NULL

 

Select * from person right outer join card on person.cardID=card.id;

 

#####  Full join（全外连接）

Select * from person full join card on person.cardID=card.id;

mySQL 不支持全外连接

​                               

其实就相当于：

Select * from person left outer join card on person.cardID=card.id

Union

Select * from person right outer join card on person.cardID=card.id;

 

其结果为：

+------+------+--------+------+--------+

| id  | name | cardID | id  | name  |

+------+------+--------+------+--------+

| 1  | 张三 | 1  | 1  | 饭卡  |

| 2  | 李四 | 3  | 3  | 农行卡 |

| 3  | 王五 | 6  | NULL | NULL  |

| NULL | NULL | NULL | 2 | 建行卡 |

| NULL | NULL | NULL | 4 | 工商卡 |

| NULL | NULL | NULL | 5 | 邮政卡 |

+------+------+--------+------+--------+

 

**MySQL的事务**

Mysql中，事务其实是一个最小的不可分割的工作单元，事务能够保证一个业务的完整性。

比如银行转账：

```
-- a -> -100
UPDATE user set money = money - 100 WHERE name = 'a';
 
-- b -> +100
UPDATE user set money = money + 100 WHERE name = 'b';
```

在实际项目中，假设只有一条 SQL 语句执行成功，而另外一条执行失败了，就会出现数据前后不一致。

因此，在执行多条有关联 SQL 语句时，**事务**可能会要求这些 SQL 语句要么同时执行成功，要么就都执行失败

 

Mysql中如何控制事务？

\1.   Mysql默认是开启事务的

Select @@autocommit;

默认事务开启的作用？

当我们去执行一个sql语句的时候，效果会立刻显现出来，且不能回滚。

回滚：撤销前面执行的事务语句。

语句：rollback;

 

Create database bank;

Create table user(

Id int primary key,

Name varchar(20),

Money int

);

 

 

想可以撤回，设置mysql自动提交为false。

Set autocommit=0;

再查看它的autocommit状态，就是0了；

+--------------+

| @@autocommit |

+--------------------+

|    0   |

+--------------------+

这时，就可以进行撤回操作：

Insert into user values(2,'b',1000);

Select * from user;

+----+------+-------+

| Id | Name | Money |

+----+------+-------+

| 2 | b  | 1000 |

+----+------+--------+

Rollback;

Select * from user;

**显示：**

Empty set (0.00 sec)

 

那么在事务关闭（也就是每提交一次任务，想要进行无法撤回操作），需要在每输入一条指令后，发送：

Commit; //手动提交数据

这时，rollback也无法撤回。

 

体现了**事务的特性：持久性**。这时rollback无用。

 

自动提交：

@@autocommit=1；

手动提交：

Commit;

事务回滚：

Rollback；

 

这时候，利用事务的特性，我们可以使银行转账同时成功同时不成功：

```
UPDATE user set money = money - 100 WHERE name = 'a';
UPDATE user set money = money + 100 WHERE name = 'b';
```

Select * from user;

+----+------+-------+

| Id | Name | Money |

+----+------+-------+

| 1 | a  |  900 |

| 2 | b  | 1100 |

+----+------+-------+

转账不成功，则rollback

这时候我们再查看

Select * from user;

+----+------+-------+

| Id | Name | Money |

+----+------+-------+

| 1 | a  | 1000 |

| 2 | b  | 1000 |

+----+------+-------+

可以看到事务回滚了。

 

在autocommit打开的情况，想要让事务依旧具有可撤回（原子性）性质，就使用begin语句

如：

BEGIN;

Update user set money=money-100 where name ='a';

Update user set money=money+100 where name ='b';

 

或者：

Start transaction;

Update user set money=money-100 where name ='a';

Update user set money=money+100 where name ='b';

 

 

### **事务**

事务的四大特征：

A atomicity原子性：事务是最小的单位，不可以再分割

C consistency一致性：事务要求，同一事务中的sql语句，必须保证同时成功或者同时失败

I isolation隔离性：事务1和事务2之间是具有隔离性的

D durability持久性：事务一旦结束（commit），就不可以返回（rollback失败）；

 

事务开启：

\1.   修改默认提交 set @@autocommit =1;

\2.   Begin;

\3.   Start transaction;

事务提交

Commit;

事务回滚

Rollback;

 

#### 四种隔离级别

1. Read uncommitted;  读未提交的（脏读、不可重复读、幻读都可能出现）
2. Read committed;   读已经提交的（解决了脏读、但是不可重复读、幻读问题没解决）
3. **Repeatable read**;   可以重复读（脏读和不可重复读解决，幻读没解决）mysql默认
4. Serializable;     串行化（都解决，但是性能差）



脏读：对两个事务，一个事务A读到了被另外一个事务B更新，但还没提交的字段。之后另外那个事务B回滚，那么A读到的内容是临时且无效的。

不可重复读：对两个事务A，B，A读取了一个字段，然后B更新了该字段。之后A再次读取同一个字段，值不一样。

幻读：两个事务A,B，A从一个表中读取了5个数据，想插入，这时候B在表中插入了新的数据6，这时候B没有结束.

A插入数据第6条数据插入不进去，但是表中显示只有5条数据？？



 

1. Read uncommitted;  读未提交的

如果有事务a，和事务b，

A事务对数据进行操作，在操作的过程中，如果事务没有被提交，但是b可以看见a操作的结果。

 

Bank数据库 user表

Insert into user values(3, '小明',1000);

Insert into user values(4, '淘宝',1000);

 

如何查看数据库的隔离级别？

Mysql 8.0

系统级别的

Select @@global.transaction_isolation;

会话级别的

Select @@global.transaction_isolation;

 

如何修改隔离级别？

SET GLOBAL TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;

Select @@global.transaction_isolation;

+--------------------------------+

| @@global.transaction_isolation |

+--------------------------------+

| READ-UNCOMMITTED        |

+--------------------------------+

 

转账：小明在淘宝店买鞋子：800块钱

小明->成都ATM

淘宝店->广州 ATM

 

start transaction;

update user set money=money-800 where name='小明';

update user set money=money+800 where name='淘宝';

 

给淘宝店打电话，说你去查下是否到账；

+----+------+-------+

| Id | Name | Money |

+----+------+-------+

| 1 | a  |  800 |

| 2 | b  | 1200 |

| 3 | 小明 |  200 |

| 4 | 淘宝 | 1800 |

+----+------+-------+

发货，晚上请女朋友吃好吃的，花了1800

发现钱不够，店主：？？？

因为小明在成都：rollback；

 

这表明：

如果两个不同的地方都在进行操作，如果事务a开启之后，它的数据可以被其他事务读取到。

淘宝店在广州可以读取到成都的数据。

但实际上此时小明还没提交订单。

 

##### 脏读

这样就会出现（脏读）：一个事务读到了另外一个事务没有提交的数据，叫做脏读。

实际开发不允许脏读出现。

 

2. Read committed;   读已经提交的

set global transaction isolation level read committed;

Select @@global.transaction_isolation;

+--------------------------------+

| @@global.transaction_isolation |

+--------------------------------+

| READ-COMMITTED     |

+--------------------------------+

 

一个事务能够读到另外一个事务已经提交的数据。

小张：银行的会计

start transaction;

select * from user;

+----+------+-------+

| Id | Name | Money |

+----+------+-------+

| 1 | a  |  800 |

| 2 | b  | 1200 |

| 3 | 小明 | 1000 |

| 4 | 淘宝 | 1000 |

+----+------+-------+

 

小张上厕所去了……抽烟

小王：输入操作

start transaction;

insert into user values(5, 'c',100);

commit;

 

+----+------+-------+

| Id | Name | Money |

+----+------+-------+

| 1 | a  |  800 |

| 2 | b  | 1200 |

| 3 | 小明 | 1000 |

| 4 | 淘宝 | 1000 |

| 5 | c  |  100 |

+----+------+-------+

 

小张：上完厕所，抽完烟回来了。

select avg(money) from user;

+------------+

| avg(money) |

+------------+

|  820.0000 |

+------------+

money不是他上厕所之前的平均值？？？

 

虽然我只能读到另外一个事务提交的数据，但还是会出问题：

读取同一个表的数据，发现前后不一致；

这叫做：

##### **不可重复读现象**；

read commited

 

3. Repeatable read; 可以重复读

set global transaction isolation level repeatable read;

Select @@global.transaction_isolation;

+--------------------------------+

| @@global.transaction_isolation |

+--------------------------------+

| REPEATABLE-READ        |

+--------------------------------+

 

张全蛋-成都

start transaction;

 

王尼玛-北京

start transaction;

 

张全蛋-成都

insert into user values(6, 'c',1000);

张全蛋能看到自己的信息，6-c-1000

 

王尼玛-北京

insert into user values(6, 'c',1000);

王尼玛看到列表中只有5个人，但是他插入第6条数据插不进去？？

系统报错，6已经存在。

 

##### 幻读

这种现象就叫做幻读！！

事务a和事务b同时操作一张表，事务a提交的数据，也不能被事务b读到，就可以造成幻读。

 

4. Serializable;     串行化

修改隔离级别为串行化

set global transaction isolation level Serializable;

Select @@global.transaction_isolation;

+--------------------------------+

| @@global.transaction_isolation |

+--------------------------------+

| SERIALIZABLE      |

+--------------------------------+

 

张全蛋-成都

start transaction;

 

王尼玛-北京

start transaction;

 

张全蛋-成都

insert into user values(6, 'c',1000);

commit;

 

王尼玛-北京

select * from user;

可以查询

 

张全蛋-成都

start transaction;

 

王尼玛-北京

王尼玛在插入时，会一直卡顿

insert into user values(7, 'd',1000);

ERROR 1205 (HY000): Lock wait timeout exceeded; try restarting transaction

 

当user表被另外一个事务操作时，其他事务里面的写操作，是不可以进行的。

进入排队状态（串行化），直到张全蛋的事务结束后，王尼玛的写入操作才能执行。

在没有等待超时的情况下。

 

张全蛋

commit;

 

王尼玛

Query OK, 1 row affected (0.00 sec)

立刻执行成功

 

串行化的问题：性能非常差

性能排序：

READ-UNCOMMITTED > READ-COMMITTED > REPEATABLE-READ > SERIALIZABLE;

 

mysql 默认隔离级别是 REPEATABLE-READ

 

但是性能越好，出现的问题越多。



### 索引

用select from where group by……having……order by 效率太低

因此想要建立索引

 

假设我们想查找91的数据：遍历4次才能找到

​                               ![image-20200824161633012](MySQL.assets\image-20200824161633012.png)

但是当我们数据量上万时，如何才能快速查询？

解决：**建立索引**

索引是一种数据结构

如，建立二叉树：每次和根节点作比较，如果比根节点大，去右子树，如果比根节点小，去左子树

 ![image-20200824161649247](MySQL.assets\image-20200824161649247.png)

但树里面存放的实际是指针。

 

索引的优势劣势

索引的优势：

\1.    类似于书籍的目录索引，提高数据检索的效率，降低数据的IO成本。

\2.    通过索引对数据进行排序，降低数据的排序的成本，降低CPU的消耗。

 

劣势

\1.    实际上索引也是一张表，该表保存了主键和索引字段。并指向实体类的记录，所以索引列也是要占用空间的。

\2.    虽然索引大大提高查询效率，也降低更新表的速度，如对表进行insert、update、delete。

####  索引结构

索引是在MySQL的存储引擎层中实现

BTREE索引：最常见的索引类型，大部分索引都支持B树索引。

HASH索引：memory引擎支持，使用场景简单。

R-tree索引（空间索引）：空间索引是MyISAM引擎的一个特殊索引类型



##### BTREE结构

又叫多路平衡搜索树，一颗m叉的BTree特性：

树中每个节点最多包含m个孩子；

除根节点与叶子节点外，每个节点至少有[ceil(m/2)]个孩子。(ceil表示向上取整)

若根节点不是叶子节点，则至少有两个孩子。

<u>所有的叶子节点都在同一层。</u>

每个非叶子节点由n个key与n+1个指针组成，其中[ceil(m/2)-1<=n<=m-1]



以5叉BTree树为例，key的数量，[ceil(m/2)-1<=n<=m-1]，所以2<=n<=4。当n>4时，中间节点分裂到父节点，两边节点分裂。

插入CNGAHEKQMFWLTZDPRXYS数据为例：

![image-20200824145802099](MySQL.assets\image-20200824145802099.png)

![image-20200824145856287](MySQL.assets\image-20200824145856287.png)

![image-20200824145935112](MySQL.assets\image-20200824145935112.png)

![image-20200824150000557](MySQL.assets\image-20200824150000557.png)

##### B+TREE结构

B+Tree为BTree的变种，B+Tree与BTree的区别为：

1）n叉B+Tree树最多含有n个key，而BTree最多含有n-1个key。

2）B+Tree的叶子节点保存所有的key信息，依key大小顺序排列。

3）所有的非叶子节点都可以看作是key的索引部分。

![image-20200824152800668](MySQL.assets\image-20200824152800668.png)

B+树，所有的数据都存放在叶子结点中。

非叶子节点的数只是起到索引作用。

B+Tree的优点：不管查询什么，都必须走到叶子节点。

因此查询稳定性好很多

![image-20200824153018743](MySQL.assets\image-20200824153018743.png)

#### 索引分类与语法

1) 单值索引：一个索引当中只包含一个列，一个表可以有多个单列索引。

2) 唯一索引：索引列的值必须唯一，但允许有空值。

3) 复合索引：一个索引包含多个列。



索引语法

创建表的时候，可以同时创建，也可以随时增加新索引。

准备环境：

create database demo01 default charset=utf8mb4;

use demo01;

create table city(

city_id int primary key auto_increment,

city_name varchar(50) not null,

country_id int not null,

foreign key (country_id) references country(country_id)

)ENGINE = InnoDB DEFAULT CHARSET=utf8;



create table country(

country_id int primary key auto_increment,

country_name varchar(30) not null

)ENGINE =InnoDB DEFAULT CHARSET=utf8;



insert into city values(1,'西安',1);

insert into city values(2,'NewYork',2);

insert into city values(3,'北京',1);

insert into city values(4,'上海',1);

insert into country values(1,'China');

insert into country values(2,'America');

insert into country values(3,'Japan');

insert into country values(4,'UK');



##### 索引的操作

###### 创建索引

语法：

create (uique\fulltext\spatial) index <u>idx_name</u>（idx_name表示索引名称）

using index_type (索引类型，不指定的话默认B+树)

on table_name(字段名\属性名)



mySQL中，创建表时，主键有索引（主键索引）

对city表中的name**创建索引**：

create index idx_city_name on city(city_name);



**查看**创建好的索引：

show index from city\G;

*************************** 1. row ***************************
        Table: city
   Non_unique: 0
     Key_name: PRIMARY
 Seq_in_index: 1
  Column_name: city_id
    Collation: A
  Cardinality: 3
     Sub_part: NULL
       Packed: NULL
         Null:
   Index_type: BTREE
      Comment:
Index_comment:
      Visible: YES
   Expression: NULL
*************************** 2. row ***************************
        Table: city
   Non_unique: 1
     Key_name: country_id
 Seq_in_index: 1
  Column_name: country_id
    Collation: A
  Cardinality: 2
     Sub_part: NULL
       Packed: NULL
         Null:
   Index_type: BTREE
      Comment:
Index_comment:
      Visible: YES
   Expression: NULL
*************************** 3. row ***************************
        Table: city
   Non_unique: 1
     Key_name: idx_city_name
 Seq_in_index: 1
  Column_name: city_name
    Collation: A
  Cardinality: 4
     Sub_part: NULL
       Packed: NULL
         Null:
   Index_type: BTREE
      Comment:
Index_comment:
      Visible: YES
   Expression: NULL
3 rows in set (0.00 sec)



###### 删除索引

drop index idx_city_name on city;

drop index 索引表名称 on 表名称;



**alter命令**

添加一个主键，索引值唯一，且不能为空：

alter table 表名 add primary key 索引名(属性名\列名\字段名）;



添加一个唯一索引，索引值唯一，可以为空：

alter table 表名 add unique 索引名（字段名);



添加一个普通索引，索引值可以出现很多次。

alter table 表名 add index 索引名（字段名）;



指定索引为fulltext，用于全文索引：

alter table 表名 add fulltext 索引名（字段名）; 



#### **索引设计原则**

· 对查询频次比较高，且数据量大的表建立索引。

· 索引字段的选择，最佳候选列应当从where子句的条件中提取，

· 尽量使用**唯一索引，**区分度越高，使用索引的效率越高

· 索引数量不是多多益善，索引越多，维护索引的代价也越高

· 尽量使用短索引，提升访问的I\O效率

· 利用最左前缀，N个列组合而成的组合索引，

如：

create index idx_name_email_status on tb_seller(name,email,status);

相当于：

对name创建索引;

对name,email创建索引

对name,email,status创建索引



### 视图

视图是一种虚拟存在的表，并不在数据库中实际存在。

视图是一条select语句执行后返回的结果集。

因此主要是创建这条sql查询语句上。

视图的优势：

简单：使用视图的用户不需要关心后面对应的表结构，关联条件和筛选条件。

安全：使用视图的用户只能访问他们被允许查询单结果，某些不想被用户看到的行和列可以不显示出来

数据独立：表的结构变化，对视图影响不大

**创建视图：**

create [or replace] [alcorithm={undefined|merge|temptable}]

 view 视图名 [列名\字段名，如果不填则全部显示] 

as select语句；（这其中，[]围起来的都是可选内容

如：

select c.*,t.country_name from city c,country t where c.country_id = t.country_id;

create view view_city_country as select c.*,t.country_name from city c,country t where c.country_id = t.country_id;

前面说过，视图是虚拟的表，因此，我们也可以对视图做表相同的动作，如

select * from  view_city_country;

当对视图进行更新操作：

update  view_city_country set city_name = '西安市' where city_id =1;

实际上对基础表也进行了更新，

此时再select * from city;

可以看到

+---------+-----------+---------------+
| city_id | city_name | country_id |
+---------+-----------+---------------+
|       1 | 西安市       |          1 |
|       2 | NewYork   |          2 |
|       3 | 北京           |          1 |
|       4 | 上海           |          1 |
+---------+-----------+----------------+

但是，不建议更新视图，视图只用作查询比较好。



**查看视图**

show tables;

也可以查看视图（因为视图本来就是虚拟的表）

还有 

show create view view_city_country;

这句可以查看到创建视图时候的语句。



**删除视图**

drop view if exists 视图名;

drop view if exists view_city_country;



### 存储过程和函数

存储过程和函数 是事先经过编译并存储在数据库中的一段SQL语句的集合。

存储过程和函数的区别：函数必须有返回值，存储过程没有。

函数：有返回值的过程

过程：没有返回值的函数



创建存储过程

create procedure procedure_name ([需要的参数])

begin

--SQL语句

end;



分隔符的问题：

MySQL的默认分隔符为;

将；分隔符替换为$的语句：delimiter $

这之后，执行语句都要用$作为结尾。



```
delimiter $
create procedure pro_test1()
begin
select 'Hello Mysql';
end$
delimiter ;
```



**调用存储过程**

call procedure_name();

call pro_test1();



**查询存储过程**

```
--查询db_name数据库中所有的存储过程
select name from mysql.proc where db='db_name';
select name from mysql.proc where db='demo01';

--查看存储过程的状态信息
show procedure status\G;

--查询某个存储过程的定义
show create procedure test.pro_test1\G;

```

删除存储过程

drop procedure [if exists] sp_name;

drop procedure pro_test1;



#### 语法

存储过程可以变成，可以使用变量，表达式，控制结构完成复杂功能

##### **变量**

**定义**

declare

定义一个局部变量，变量的作用范围只在begin end间。

declare var_name[,…] type []

```
delimiter $
create procedure pro_test1()
begin
declare num int default 10;
select concat('num的值为',num);
end$
```

default 的意思是设置默认值。



set 赋值

赋值使用set，赋常量或赋表达式

set var_name = expr [ , var_name = expr ]……

```
delimiter $
create procedure pro_test2()
    -> begin
    -> declare num int default 0;
    -> set num = num + 10;
    -> select num;
    -> end$
delimiter ;
```

也可以用select……into方式进行赋值操作：

```
delimiter $
create procedure pro_test3()
begin
declare countnum int;
select count(*)into countnum from city;
//让countnum和city的行数相等
select concat('city表中的记录数为：',countnum);
end$
delimiter ;
```



###### if条件判断

语法结构

```
if search_condition then statement_list
        [elseif search_condition then statement_list]…
        [else statment_list]
end if;

delimiter $
create procedure pro_test3()
begin
declare height int default 175;
declare description varchar(50) default '';
if height >=180 then
set description ='身材高挑';
elseif height >=170 and height <180 then
set description ='标准身材';
else
set description ='一般身材';
end if;
select concat('身高 ',height,' 对应的身材类型为：',description);
end$
```



###### 传递参数

in： 代表改参数可以作为输入，需要调用放传入值

out： 代表该参数作为输出，也就是该参数可以作为返回值

INOUT：既可以作为输入，也可以作为输出。



**in-输入**

语法格式：create procedure procedure_name([in/out/inout] 参数名 参数类型)

如果不输入in/out/inout，默认为in

```
create procedure pro_test4(in height int)
begin
declare description varchar(50) default '';
if height >=180 then
set description ='身材高挑';
elseif height >=170 and height <180 then
set description ='标准身材';
else
set description ='一般身材';
end if;
select concat('身高 ',height,' 对应的身材类型为：',description);
end$

call pro_test4(198)$
```



**OUT-输出**

```
create procedure pro_test5(in height int,out description varchar(10))
begin
if height >=180 then
set description ='身材高挑';
elseif height >=170 and height <180 then
set description ='标准身材';
else
set description ='一般身材';
end if;
end$
```

调用会返回一个值

这个值需要储存在变量中，因此需要创建一个变量用于储存返回值。

```
call pro_test5(188,@description)$
```

此处，**@description**中的**@表示创建一个用户会话变量**

用户会话变量的意思是：当我当前会话关闭，变量释放空间。类似全局变量

当前面是两个@@，代表系统变量

然后我们查询它的值

```
select @description
```

+--------------+
| @description |
+--------------+
| 身材高挑     |
+--------------+



###### case结构

方式一：

```
CASE case_value
	WHEN when_value THEN statement_list
	[WHEN when_value THEN statement_list]…
	[ELSE statement_list]
END CASE;

方式二：
CASE
	WHEN search_condition THEN statement_list
	[WHEN search_condition THEN statement_list]…
	[ELSE statement_list]
END CASE;
```

--给定一个月份，然后计算出月份所在的季度

```
create procedure pro_test7(mon int)
begin
declare result varchar(10);
case
when mon>=1 and mon<=3 then
set result='第一季度';
when mon>=4 and mon<=6 then
set result='第二季度';
when mon>=7 and mon<=9 then
set result='第三季度';
when mon>=10 and mon<=12 then
set result='第四季度';
end case;
select concat('传递的月份为：',mon,'，计算的结果为：',result) as content;
end$
```



###### while循环

语法结构：

```
while serch_condition do
statement_list
end while;
```

--计算从1加到n的值--累加

```
create procedure pro_test8(n int)
begin
declare total int default 0;
declare num int default 1;
while num<=n do
set total = total + num;
set num = num +1;
end while;
select total;
end$
```

**repeat 结构**

满足条件，则退出循环

语法结构：

```
REPEAT
statement_list
UNTIL search_condition
END REPEAT;
```

--计算从1加到n的值（repeat）

```
create procedure pro_test9(n int)
begin
declare num int default 1;
declare total int default 0;
repeat
set total = total +num;
set num = num + 1;
until num>n
end repeat;
select total;
end$
```



loop语句

LOOP实现简单的循环，退出循环通过LEAVE语句实现，语法结构：

```
[begin_label:]LOOP
[IF search_condition then
leave begin_label]
statement_list
END LOOP [end_label]
```

--计算从1加到n的值（loop）

```
create procedure pro_test9(n int)
begin
    declare num int default 1;
    declare total int default 0;
    c:loop
        if num>n then
            leave c;
        end if;
        set total = total +num;
        set num = num + 1;
    end loop c;
    select total;
end$
```



#### 游标

游标 用来存储查询结果集的数据类型。

声明光标：

```
DECLARE cursor_name CURSOR FOR select_statement;
```

游标中存储了这个select语句的结果。

OPEN光标：

```
OPEN cursor_name;
```

FETCH光标：

```
FETCH cursor_name INTO var_name [, var_name]…
```

CLOSE光标：

```
CLOSE cursor_name；
```

示例：初始化脚本：

```
create table emp(
id int(11) not null auto_increment,
name varchar(50) not null comment '姓名',
age int(11) comment '年龄',
salary int(20) comment '薪水',
primary key (id)
)engine = innodb default charset=utf8;

insert into emp values(null,'金毛狮王',55,3800),(null,'白眉鹰王',60,4400),(null,'青翼蝠王',38,2800),(null,'紫衫龙王',42,1800);
```

--查询emp表中数据，并逐行获取进行展示

通过循环，获取游标数据。

```
create procedure pro_test02()
begin
declare eid int(11);
declare ename varchar(50);
declare eage int(11);
declare esalary int(20);
declare has_data int default 1;

DECLARE checkData CURSOR FOR select * from emp;
DECLARE EXIT HANDLER FOR NOT FOUND set has_data = 0;//对游标结束的声明，必须在游标之后。意思是当游标结束时，把has_data赋值为0

OPEN checkData;

repeat
FETCH checkData INTO eid,ename,eage,esalary;
select concat('id=',eid,',name=',ename,',age=',eage,',薪资为：',esalary);
until has_data =0
end repeat;

CLOSE checkData;
end$
```

#### 存储函数

语法结构:

```
CREATE FUNCTION function_name([param type ...])
RETURNS type
BEGIN
...
END;
```

案例：--定义一个存储函数，获取满足条件（city）的总记录数。

```
create function fun1(countryId int)
returns int
begin
declare cnum int
select count(*) into cnum from city where country_id = countryId;
return cnum;
end$
```

对函数进行调用，与过程不同。

过程调用：

```
call procedure_name;
```

函数调用：

```
select fun1(1);
```

过程删除：

```
drop procedure procedure_name;
```

函数删除：

```
drop function function_name;
```



### 触发器

触发器与表有关，指在insert/update/delete之前或之后，触发并执行触发器中定义的SQL语句集合。

触发器特性可以协助应用在数据库端确保**数据的完整性，日志记录，数据校验**等操作。

别名 OLD 和NEW来引用触发器中，发生变化的记录内容。

只支持行级触发，不支持语句级触发。

触发器类型             new和old的使用

insert型触发器       new表示将要或者已经新增的数据

update型触发器     old表示修改之前的数据，new表示将要或已经修改后的数据。

delete型触发器       old表示将要或者已经删除的数据



#### 创建触发器

语法结构：

```
create trigger trigger_name  //trigger意思是触发器
before/after insert/update/delete
on tbl_name
[for each row]  //表示操作的是行级触发器

begin
trigger_stmt
end;
```

示例

需求

```
通过触发器，记录emp表的数据变更日志，包含增加，修改，删除；
delimiter $
```

创建一张日志表：

```
delimiter $
create table emp_logs(
id int(11) not null auto_increment,
operation varchar(20) not null comment '操作类型，insert/update/delete',
operate_time datetime not null comment '操作时间',
operate_id int(11) not null comment '操作表的ID',
operate_params varchar(500) comment '操作参数',
primary key(id)
)engine = innodb default charset = utf8;
```

--通过触发器记录emp表的数据变更日志，包含增加，修改，删除；

```
delimiter $
create trigger emp_insert_trigger
after insert
on emp
for each row

begin
insert into emp_logs(id,operation,operate_time,operate_id,operate_params) values(null,'insert',now(),new.id,concat('插入后（id:',new.id,',name:',new.name,',age:',new.age,',salary:',new.salary,')'));
end$
```

new,引用的是一行，因此想要引用new中的id，是new.id

插入两条数据试试：

```
insert into emp values(null,'张无忌',30,6800)$
insert into emp values(null,'赵敏',29,5100)$
```

--触发器（修改操作）

```
delimiter $
create trigger emp_update_trigger
after update
on emp
for each row

begin
insert into emp_logs(id,operation,operate_time,operate_id,operate_params) values(null,'update',now(),new.id,concat('更新后（id:',new.id,',name:',new.name,',age:',new.age,',salary:',new.salary,')'));
insert into emp_logs(id,operation,operate_time,operate_id,operate_params) values(null,'update',now(),old.id,concat('更新前（id:',old.id,',name:',old.name,',age:',old.age,',salary:',old.salary,')'));
end$

```

--触发器（删除操作）

```
delimiter $
create trigger emp_delete_trigger
after delete
on emp
for each row

begin
insert into emp_logs(id,operation,operate_time,operate_id,operate_params) values(null,'delete',now(),old.id,concat('删除前（id:',old.id,',name:',old.name,',age:',old.age,',salary:',old.salary,')'));
end$
```

删除此触发器：

```
drop trigger emp_delete_trigger$
```





# JDBC

前端：html、css、JavaScript

框架 = 反射 + 注解 +

JDBC是java和众多数据库之间的接口。

数据库里每条记录，就是java中每一个new的对象

JDBC程序编写步骤

![image-20200831092349738](MySQL.assets/image-20200831092349738.png)

![image-20200831092706445](MySQL.assets/image-20200831092706445.png)

### 获取数据库连接

获取jdbc驱动包（mysql），得到mysql-connector-java-8.0.21.jar

然后将它导入项目下的lib中。并build path



URL

JDBC URL标准由三部分组成。各部分间用冒号分割。

jdbc：子协议：子名称

协议：JDBC URL中的协议总是jdbc

子协议：子协议用于标识一个数据库驱动程序

子名称：一种标识数据库的方法。可以根据不同的子协议而变化。用子名称的目的是为了定位数据库。包含主机名（服务端的ip地址），端口号，数据库名。

如：![image-20200831100128416](MySQL.assets/image-20200831100128416.png)











# 计算机网络

问题：每层代表性协议，分别干什么？IP和MAC地址干什么？

http介绍、如何握手

为什么tcp三次握手四次挥手，中间状态？



### 计算机网络体系结构

为什么要分层？

1. 发起通信的计算机必须将通信的通路进行激活。
2. 告诉网络如何识别目的主机。
3. 查明目的主机是否开机，并且与网络连通
4. 发起通信的计算机要弄清，对方计算机的文件管理程序是否做好准备工作
5. 确保差错和意外可以解决。

协议就是：一个封装，一个用封装方法来拆分。

接口：两层的关节

分层原则：

1. 每层相互独立，每层只实现一种相对独立的功能

2. 每层之间界面自然清晰

3. 结构上可分割开

4. 保持下层对上层的独立性，上层单向使用下层提供的服务。

5. 整个分层结构应该能促进标准化工作。





OSI七层模型

分层之后可以标准化，每一层之间不会互相影响，降低依赖。

有**应用层、表示层、会话层、运输层、网络层、数据链路层和物理层**。

中间系统，没有应用层、表示层、会话层、传输层

只有物理层、数据链路层和网络层。

中间系统时，是在传输过程中，去识别下一跳给谁。

![image-20200824171952026](MySQL.assets\image-20200824171952026.png)



**五层协议**

应用层、传输层、网络层、数据链路层和物理层。

- **应用层** ：为特定应用程序提供数据传输服务，例如 HTTP、DNS 等协议。数据单位为报文。
- **传输层** ：为进程提供通用数据传输服务。由于应用层协议很多，定义通用的传输层协议就可以支持不断增多的应用层协议。运输层包括两种协议：传输控制协议 TCP，提供面向连接、可靠的数据传输服务，数据单位为报文段；用户数据报协议 UDP，提供无连接、尽最大努力的数据传输服务，数据单位为用户数据报。TCP 主要提供完整性服务，UDP 主要提供及时性服务。
- **网络层** ：为主机提供数据传输服务。而传输层协议是为主机中的进程提供数据传输服务。网络层把传输层传递下来的报文段或者用户数据报封装成分组。
- **数据链路层** ：网络层针对的还是主机之间的数据传输服务，而主机之间可以有很多链路，链路层协议就是为同一链路的主机提供数据传输服务。数据链路层把网络层传下来的分组封装成帧。
- **物理层** ：考虑的是怎样在传输媒体上传输数据比特流，而不是指具体的传输媒体。物理层的作用是尽可能屏蔽传输媒体和通信手段的差异，使数据链路层感觉不到这些差异。
- 

**TCP/IP**

应用层、运输层、网际层和网络接口层。

相当于五层协议中数据链路层和物理层合并为网络接口层。

![image-20200824155515072](MySQL.assets\image-20200824155515072.png)

数据在各层之间传递的过程：

在向下的过程中，添加下层协议所需要的首部或尾部，在向上的过程中，不断拆开首部和尾部。



### 物理层

定义接口的标准

1. 机械特性（物理连接的规格、借口形状、引线数目、引脚数量和排列）

2. 电气特性，电压范围，传输速率和距离限制

3. 功能特性：某线上电平意义，接口部件信号线的用途

三种通信方式：

单工、半双工和全双工

单工：只有一个方向的通信

半双工：两台主机可以发送也可以接受，但不能同时

全双工：可以发送可以接受，也可以同时发送接受。



### 数据链路层

数据每次传输，都是得到mac地址，判断IP地址，判断下一跳给谁，然后封装后传输。

数据链路层 

三个问题

①**封装成帧**

②**透明传输**-控制字符前加转义字符

③**差错控制**-crc差错检测技术，fcs帧检验序列，出现错误，让服务器重传。

解决封装成帧的问题

在一段数据的头和尾添加首部和尾部。

头和尾都有控制字符，来识别这一帧的完整性。

这些控制字符如果出现在数据中，会在前面添加一个转义字符（就跟java中在\u前面再添加一个\一样）

帧检验序列FCS：通过循环冗余检验来进行，相同的取0，不同的取1，最后得到余数就是FCS。用来判断帧是否漏了或错了。

CRC差错检测技术，如果出现错误，那么就要求另外一个计算机重传。



ppp协议帧格式

F     A     C  +  协议  +        信息部分       +  FCS  +  F

7E  FF  02    2字节     不超过1500字节   2字节   1字节

协议表示里面装的是什么



ppp协议：

满足的要求：简单、封装成帧、透明性、差错检验、支持多种网络层协议、检测连接状态、最大传送单元、数据压缩协商、网络层地址协商



以太网的CSMA/CD协议：

“**多点接入**”，许多计算机以多点接入的方式连接在一根总线上，

“**载波监听**”，每一站在发送数据前检测一下总线上是否有其他计算机在发送数据（总线上的电压是否增加），如果有，就暂停，再等下一个随机事件发生。



MAC的硬件地址（MAC地址）

局域网中，硬件地址又称物理地址，或MAC地址，48位。它是全球唯一的。

每个生产网络设备的厂家都要先申请一个厂家地址，三个字节（24位）



**wireshark抓包工具**

抓包工具查看网络问题，如果网络中有一台计算机一直在发送广播，网络就一直冲突，那么网络上其他计算机无法进行连接。

抓包工具里面可以看到各个层的协议内容、数据内容。



生成树算法：避免环的产生，有广播风暴。

先选一个根结点（按照优先级），然后每次按照端口优先级去找最近的结点，连通，当所有设备都能够连通时，其他端口阻断。



### 网络层

网络层向运输层提供两种服务：

· 虚电路服务：可靠性由网络来保证，管理员需要提前配置好路径。从一个点到另外一个点，只有一条配置好的路径。每个分组使用短虚电路号

· 数据报服务：H1计算机发给H2计算机的数据报，随机选择一条路径走。某条路断了，就选择另外一条路。每个分组有完整的终点地址（像快递的起始地址）。



默认网关：是每次计算机访问其他网络时，都要去寻找本网段的网关，经过它访问别的网站。



####  IP协议

地址解析协议ARP

逆地址解析协议RARP

网络控制报文协议ICMP

网际组管理协议IGMP

IPV4协议最底层是ARP协议

ICMP和	IGMP协议都依赖于IPV4协议



##### 网络地址

二进制               十进制

10000000		128

11000000		192（128+64）

11100000		224（128+64+32）

11110000		240（128+64+32+16）

11111000		248（128+64+32+16+8）

11111100		252（128+64+32+16+8+4）

11111110		254（128+64+32+16+8+4+2）

11111111		255（128+64+32+16+8+4+2+1）



A类地址：0-127（二进制码开头是10），第一个可用的网络号：1，最后一个可用的网络号：126（因为127.0.0.1是本机的localhost）每个网络最大主机数是255×255×255

A类地址子网掩码是**255.0.0.0**，也就是第一个是网络地址

B类地址：128-191（二进制码开头是110）第一个可用的网络号：128.1，最后一个：191.255

B类地址子网掩码是**255.255.0.0**，也就是前两个是网络地址，后面是hostID

C类地址：192-223（二进制码开头是1110）第一个可用的网络号：192.0.1，最后一个：223.255.255

C类地址子网掩码是**255.255.255.0**，前三个是网络地址，后面是hostID

D类地址：224-240……



特殊的地址：

127.0.0.1 本地环回地址

169.254.0.0

保留的私网地址

10.0.0.0

172.16.0.0 —— 172.31.0.0

192.168.0.0 —— 192.168.255.0



子网掩码的作用：

跟IP地址做与运算，保留的部分是网络地址。

如果网络部分相同，证明它们应该在同一网关，那么就不用传输到网络上去了，直接在网关寻找就行。



子网划分

主机不能全为1，也不能全为0。

主机部分全为1时，是一个广播

192.126.1.0，当一个单位被分到这个网段，可以使用192.126.1.0  -  192.126.1.255这么多个IP

但是单位只有200台计算机接入网络。

可以进行子网划分，如：

将子网掩码设置为255.255.255.128

这时，将一个网段分为两个子网，等分子网：

前一个网络的hostID 是192.126.1.1 - 192.126.1.126（127是广播，不能使用，1一般是网关）

后一个网络的hostID是192.126.1.129-192.126.1.254



等分四个网段：子网掩码：255.255.255.192

八个网段：子网掩码255.255.255.224

合并网段：偶数和奇数可以合并，奇数和偶数不行，如0和1可以合并，1和2不行。



两个计算机通信的过程：

A到B：数据和消息后面接着源IP和目标IP，从A电脑到路由器，封装了A的MAC地址和路由器的MAC地址；

然后路由器查表，要到目标IP地址，下一跳的MAC地址是谁，封装好到达下一跳的网关……直至找到目标IP。



ARP欺骗

同一个网段下，计算机解析网关地址，发动攻击的计算机会把自己的mac地址告诉计算机，这是网关MAC，因此计算机上网都要经过它。

ARP过程：

知道IP地址，得到MAC地址的过程 

逆ARP过程：

计算机已经有MAC地址，向网络去请求IP地址的过程。



##### 数据包首部

数据包首部TTL生存时间

代表数据包在网络中最多可以经过多少个路由器（每过一个路由器，TTL-1）

windows 系统，TTL一般默认为128.

ping一个网段，能够看到中间经过了几个路由器（返回的TTL等于多少，减去128，就是经过多少路由）

还可以查询每一跳到了哪个网关，比如第一跳：设置 ping 172.46.15.1 -i 1

这就代表将TTL设置成1。返回时下一跳网关会告诉你请求超时。



ICMP协议

类型8字节，代码8字节，检验和15字节

动态路由协议

​		RIP 周期性广播：每隔30秒告诉别的路由器，自己的网段。 最大跳数 16跳

​		不广播的时候，隔壁的路由器就认为它去世了（宕机了），就会告诉其他广播，删除这个路由。



### 传输层

应用层 http https ftp DNS SMTP PoP3 RDP

传输层 TCP UDP

网络层 IP（RIP OSPF BGP） ICMP IGMP ARP



#### UDP/TCP的应用场景

TCP  分段 编号 流量控制  建立会话  netstat -n

UDP  一个数据包能够完成数据通信的时候（比如询问DNS，百度的网址是什么) 不建立会话  多播 



传输层和应用层之间的关系

​	http 在传输层用的TCP协议+80端口

​	https = TCP + 443端口

​	ftp = TCP + 21

​	SMTP  =  TCP + 25

​	POP3  =  TCP + 110

​	RDP =  TCP + 3389

​	SQL = TCP + 1433

​	DNS = UDP + 53 or TCP + 53端口

用端口来区分服务。

查看自己计算机侦听的端口

netstat -an

这个可以通过会话查木马

因此，为了让服务器更安全，可以只开某些端口（想要什么服务，开什么端口）



防木马的一个措施：

木马特点，访问和传回数据时，端口不一致。

因此可以通过判断访问和传回数据的端口来判断，是否是木马。

通过设定端口（IP协议端口，从此端口出，从此端口入）



TCP与UDP

两个对等运输实体在通信时传送的数据单位叫做运输协议数据单元

TCP传送的协议数据单元是TCP报文段

UDP传送的协议数据单元是UDP报文或用户数据报。



#### UDP 

首部

源IP地址   目的IP地址  UDP长度  （传输层加目标IP源IP

伪首部 **源端口 目的端口** 长度 检验和 （网络层加目标端口源端口）



#### TCP协议

面向连接的传输层协议

每一条TCP连接只能有两个端点，每一条ICP连接只能是点对点的

TCP提供**可靠交付**的服务。

TCP提供全双工通信。

面向字节流



##### 可靠传输

TCP如何实现**可靠传输**？

1. 停止等待协议（超时重传）

发送数据包后，等一个RTT（往返时间）更长点的时间，如果没有收到确认，那么就对数据包进行重传。

2. 确认丢失了（A发数据包，B收到了，但返回去的确认信息丢失了）

   这时A会重传，B收到，收到重复信息后丢弃，但是返回确认信息。

3. 确认迟到（A发数据包，B收到了，但返回去的确认信息过了很久，A才收到）

   这时A什么都不做。

这个可靠传输协议被称为自动重传请求 ARQ，重传是自动进行的

停止等待协议优点很简单，但是缺点是信道利用率太低。



连续ARQ协议：

维持发送窗口

![image-20200826164505459](MySQL.assets\image-20200826164505459.png)

为了提高信道利用率，就是一直传输，从头部开始发到尾部。

然后再某个时刻，开始收到之前传输过去的确认回信。



但一个个确认还是效率太低，因此优化为累积确认。

如：A向B进行传输，12345678910……

B收到了12 45

B会向A发送确认信息：2，表示它收到了2。



##### TCP报文段首部

![image-20200826165135118](MySQL.assets\image-20200826165135118.png)

序号：表示发送的这个数据包，第一个字节，在整个文件中的第几个字节

确认号ack：B计算机给A计算机发送的，已经收到的数据，下一个字节。如：已经收到1-4字节，B计算机发的确认号就是5.

数据偏移：TCP首部不是固定长度的，数据偏移用来记录报文段多少字节后存储的数据。

窗口cwnd：接收缓存的大小（最多只能放这么多数据）

没收到确认前，滑动窗口不能改变。

SYN表示建立连接，
FIN表示关闭连接，
ACK表示响应，



超时重传时间：略大于加权平均往返时间。



##### 流量控制

TCP如何实现流量控制？

流量控制解决发送接受两端，网速不一样的问题。

通信前，B计算机告诉A计算机，接收缓存是多少。

A计算机根据B计算机发过来的接收缓存，调整自己的发送缓存。



##### 拥塞控制

TCP协议如何避免网络拥塞？

流量控制是由于AB计算机，两个的缓存处理大小不同

拥塞控制时由于整个网络的通信，是一个全局性的过程，涉及到所有的主机、所有的路由器。 

**慢开始 和拥塞避免**

一开始发送窗口比较慢，将拥塞窗口设置为1.

然后指数规律增长，到了慢开始门限值ssthresh，就停止指数增长，每次+1）

然后出现了网络拥塞（丢包）

此时计算一个新的慢开始门限ssthresh = 拥塞时的1/2，并且，发送窗口重新从1开始。



**快重传**

正常情况下，比如发5个字节，回复一个确认。

但是此时收到了 1 2,4，看到4，没有看到3，B计算机知道3已经丢失了。此时还没没收到5个字节，就发送一个丢失信息。

发现丢包，立刻发送重传信息（连续发送**三遍**）

**快恢复**

收到3个重复的确认执行快重传算法，证明网络没有拥塞，（丢包不是网络拥塞造成的）n那么重传时候的窗口，从慢开始门限ssthresh开始重传。



因此，一开始的慢开始发送窗口下限值，是拥塞窗口大小控制的，上线，是由B计算机告诉它的流量控制窗口值决定的。



##### TCP连接的三次握手

A计算机和B服务器建立连接：

1. A计算机向B计算机发送数据报： SYN同步位=1,ACK确认号是0，seq序号=x。

B服务器收到信息，知道这是主动请求建立连接的信息。

2. B服务器向A计算机发送被动打开的确认：SYN=1,ACK=1,seq序号=Y，ack=x+1；

3. A计算机，正式建立连接了，SYN=0，ACK=1.seq=x+1，ack=y+1



1和2，保证网络是畅通的。第一次，A向B发送，可以包含流量控制的窗口大小。

但是三次握手避免的情况：

A计算机向B计算机发送多次请求。每次B计算机都占用一部分缓存，用于跟A建立连接。但是迟迟没有回复，造成B服务器的资源浪费。

三次握手建立TCP连接的各状态：

![image-20200826200419594](MySQL.assets\image-20200826200419594.png)



如果计算机处于很多SYN-RCVD状态，证明有人伪造一个假地址，来攻击。

这样你的计算机发送确认回去，发现没有收到确认信息。

三次握手，保证了通信的双方，都确认对方地址的正确性。





A客户端，主动关闭：

A发一个FIN=1,seq =u的信息



B客户端，被动关闭：

当B发完数据，应用进程通知TCP释放链接

B发送信息： FIN =1,ACK=1,seq=w，ack=u+1到A



![image-20200826201429025](MySQL.assets\image-20200826201429025.png)

MSL = 2分钟

A的TIME - WAIT状态：确保A最后发给B关闭的信息被B接收了（因为没有接收，B服务器就一直处于LAST-ACK状态，造成资源浪费）



### 应用层

#### DNS服务

域名解析服务：将域名解析成IP

这就需要，每个域名，全球唯一

顶级域名：com edu net cn org gov

QQ在登陆时，自己把地址写在了后台。如果域名解析失败，就自动使用它的IP地址。



#### HTTP协议

超文本传输协议：基于TCP之上，应用层，是通用的、无状态的面向对象协议

www：world wide web

因为全球的服务，是分布式连接，可以通过点击链接，连接到别的网站。



百度引擎：将全球的域名，抓取到自己的数据库里，进行排序，每次用户搜索的时候进行匹配。



http协议是浏览器，作为http客户端，通过URL发送所有请求。web服务器根据接收道的请求后，向客户端发送响应信息。

URL：

![image-20200826202654080](MySQL.assets\image-20200826202654080.png)



http建立在TCP之上，实际上是TCP+端口，套接字

一次HTTP操作称为一个事务，其工作过程可分为四步：

（1）客户与服务器建立连接；

（2）客户向服务器提出请求；

（3）服务器接受请求，并根据请求返回相应的文件作为应答；

（4）客户与服务器关闭连接。

（5）客户端浏览器解析html内容



在浏览器地址栏键入URL，回车后经历：

浏览器向DNS服务器请求解析该URL中域名对应的IP地址。

解析出IP地址后，根据IP地址和默认端口80，和服务器建立TCP连接。





# 操作系统

中断技术

CPU收到外部信号（中断信号）后，停止当前工作，转而处理该外部事件，处理完毕后回到原来工作的中断点（断点）继续原来的工作。

通道技术

专门处理外设与内存之间的数据传输的处理机。



### 分时技术：

主机以很短的“时间片”为单位，把CPU轮流分配给每个终端使用，直到全部作业被运行完。

由于时间片很短，在终端数量不多的时候，每个终端很快重新获得CPU，使得每个终端都能得到及时响应。

等待周期= 时间片×终端数量

unix 

是第一个实用化的分时操作系统。

实现操作系统的可移植性。实现了硬件无关性。



实时操作系统

背景：实时事务：军事，工业控制，智能仪器等

要求：某些任务要优先紧急处理

特点：强调作业完成的时限

嵌入式操作系统：andriod、ucOS、ucLinux、Linux（手机上）



操作系统逻辑结构

1. 整体性：

变量都以全局变量存在



2. 层次结构

TCP/IP协议栈

![image-20200827145651342](MySQL.assets/image-20200827145651342.png)!

![image-20200827145801909](MySQL.assets/image-20200827145801909.png)

每一层 为上一层提供服务

分层原则：

与硬件相关：最底层

外部特性：高层

有利于系统维护，管理



3. 微内核结构

   操作系统 = 微内核 + 核外服务器

   微内核

   足够小，提供OS最基本的核心服务

   ①实现与硬件紧密相关的服务，实现较基本的功能；负责客户和服务器间的通信。

   

   核外服务器

   完成OS的绝大部分功能，等待应用程序提出请求。

   如进程/线程服务器，虚存服务器，设备管理服务器等。以进程的形式存在在用户态。



操作系统最基本硬件结构

1. CPU

2. 内存
3. 中断
4. 时钟



### CPU

CPU态：CPU的工作状态，对资源和指令使用权限的描述。

态的分类

**核态（kernel mode）**

能够访问所有资源和执行所有指令

管理程序/OS内核



**用户态（user mode，目态）**

仅能访问部分资源，其他资源受限

用户程序



**管态（Supervisor mode）**

介于核态和用户态之间



**用户态向核态转换：**

用户请求OS提供服务

发生中断

用户进程产生错误（内部中断）

用户态企图执行特权指令



**核态向用户态转换的情形**

一般是执行中断返回：IRET



**硬件和OS对CPU的观察**

**硬件**是按照“**态**”来区分CPU的状态

**OS**按“**进程**”来区分CPU的状态



IntelCPU的态：

Ring0~Ring3

程序段A访问程序段B时，进行权限检查（态）

程序段：请求特权级RPL

描述符特权级DPL



#### 存储器

按照存储器读写方式：

分为**RAM**和**ROM**

按照存储元的材料，分为：

半导体存储器（常作主存）

磁存储器（磁带，磁盘）

光存储器（光盘）



按与CPU的关系，分为：

主存（直接跟CPU交换信息，也就是内存）

辅存（不能直接跟CPU交换信息）



实际的存储体系：由寄存器（最快）、高速缓存（第二快，CACHE）、主存和辅存混搭而成、

CPU读取指令或数据的访问顺序：

1）访问缓存（命中，HIT）

2）访问内存（没有命中，MISS）

3）访问辅存（缺页，PAGE_FAULT)

 

### 中断

CPU对突发的外部时间的反应过程或机制

CPU收到外部信号（中断信号）后，停止当前工作，转去处理该外部事件，处理完毕后回到原来工作的中断处（断点）继续原来的工作（中断返回）。



引入中断原因：

实现并发活动：CPU和外设并发，外设收到信号，发送给CPU

实现实时处理

故障自动处理



中断源和中断类型

引起系统中断的时间，叫**中断源**。

中断类型：

1. 强迫性中断和自愿中断

     强迫性中断：程序没有预期，例：I/O、外部中断

     自愿中断：程序有预期的，如：执行访管指令

2. 外中断（中断）和内中断（俘获）

   ​	外中断又分为可屏蔽中断和不可屏蔽中断

   ​	不可屏蔽中断：中断的原因很紧要，CPU必须响应

   ​	可屏蔽中断：中断原因不紧要，CPU可以不响应



**断点**

程序中断的地方，将要执行下一指令的地址

CS:IP000000



**现场**

程序正确运行依赖的信息集合：相关寄存器



**现场的两个处理过程**

现场的保护：进入中断服务程序之前，保存在栈中。

现场恢复：退出中断服务程序后，从栈中pop出来



#### 中断响应过程：

1）识别中断源

2）保护断点和现场

3）装入中断服务程序的入口地址（CS:IP）

4）进入中断服务程序

5）恢复现场和断点

6）中断返回：IRET



#### **中断响应的实质**

交换指令执行地址

交换CPU的态

工作

​	现场保护和恢复

​	参数传递（通信）



### 操作系统启动过程

#### 实模式和保护模式

实模式（求实地址模式，REAL MODE)

程序按照8086寻址方法访问0h-FFFFFh（1MB）空间（640K，基本内存，128K，显卡显存，256K，BIOS)

寻址方式：物理地址（20位）=段地址（16位）：偏移地址（16位）

CPU单任务运行



保护模式（内存保护模式，protect mode）

寻址方式：段（32位）和偏移量（32位），寻址4GB空间。

虚拟地址，进程，封闭空间

应用程序和操作系统的运行环境都被保护



**MBR**

BIOS启动后会去读首扇区，主启动记录MBR

存放和OS启动相关的信息（Main Boot Record）

512 BYTES	

结束：0xAA55h



#### BIOS和MBR程序运行过程

（1）POST（加电启动）->CMOS （硬盘启动）->读取MBR->控制权交给MBR

（2）MBR读取分区表，找到其中活动分区，确认其他分区不是活动分区。MBR读取活动分区的第一个分区（分区引导记录PBR）并把它加载到内存中去。

（3）PBR继续控制后面的引导过程。



#### 操作系统启动过程

1）初始引导

把OS核心装入内存，并使之开始工作。

①加电，JUMP POST

②BIOS的启动程序运行

③加载MBR的引导程序

④根据参数，读取硬盘的文件到内存

⑤加载硬盘上OS内核，初始化基本参数。



**LInux启动过程**

POST->MBR->KERNEL映像->KERNEL映像边解压边加载->内核初始化->内核启动

kernel映像前端是可执行例程，有解压和初始化的引导程序。



#### Shell 脚本编程

脚本（Script）通过类似程序的方式执行具有一定逻辑顺序的命令序列完成较复杂的功能和人机交互。

脚本文件保存在文本文件中，后缀bat

脚本程序时shell命令语句的集合。



可以帮助用户安装程序

编写完脚本后，需要执行命令：

Chmod + x MyScript.sh（MyScript.sh脚本文件的名称）



shell中的语句意思：

echo：向控制台输出字符串。

case（与其他地方的switch case差不多），结尾esac



执行脚本：

①$bash first_script

②脚本开头增加

#!/bin/bash



操作系统区别于应用软件，最核心的是有进程管理和内存管理功能

### 进程

描述和管理程序的“运行过程”：进程

定义：

进程是程序在某个**数据集合**上的**一次**运行**活动**。

数据集合：软/硬件环境，多个进程共存/共享的环境



#### 特征

**动态性：**进程是程序的一次执行过程，动态产生/消亡

**并发性**：进程同其他进程一起向前推进；

**异步性**：进程按各自速度向前推进。（每个进程有自己的进度）

**独立性：**进程是系统分配资源和调度CPU的单位



进程（动态的，程序的一次执行过程）（暂存的，在内存中驻留，完成即释放）

程序（静态的，一组指令的有序集合）（长存的，在介质上长期保存，除非你卸载）（一个程序可能有多个进程）



#### 进程的状态

**运行状态**：进程已经占有CPU，在CPU上运行。

**就绪状态**：具备运行条件，但由于无CPU，暂时不能运行。

**阻塞状态**：（block、wait）因为等待某项服务完成，或没有等到信号不能运行的状态，如：系统调用、I/O操作……



转化：**就绪->运行**：进程调度

**运行->就绪**：时间片到了；或者更高优先级的进程，抢占了时间片。

**运行->阻塞**：请求操作系统为它提供服务；等待信号

**阻塞->就绪**：服务完成；信号到来



Linux中：

分为**可运行态**（就绪，和运行，合二为一）

**阻塞态**（浅度阻塞，能被信号或时钟唤醒；深度阻塞，不能被信号和时钟唤醒）

**僵死态**：进程终止运行，释放大量资源

**挂起态**

 

**进程控制块**（Process Control Block，PCB）

描述进程状态、资源和相关进程关系的数据结构

PCB是进程的标志

创建进程时创建PCB：进程撤销后PCB同时撤销

进程 = 程序 + PCB

![image-20200827171738001](MySQL.assets/image-20200827171738001.png)



#### 进程创建

​	创建一个空白PCB

​	获得并赋予进程标识符ID

​	为进程分配空间

​	初始化PCB（默认值）

​	插入相应的进程队列（新进程插入就绪队列）

伪代码：

![image-20200827172914421](MySQL.assets/image-20200827172914421.png)



**进程撤销：**

指定ID，然后进行撤销。

进程撤销的实现：递归，先撤销子进程，然后撤销父进程。



**进程阻塞**：

停止进程执行，变为阻塞

参数：

阻塞原因，不同原因有不同阻塞队列。



**进程唤醒：**

唤醒阻塞队列的某个进程

参数：ID号



Linux系统

创建进程：fork（）

函数内有fork（），代表创建子进程。返回值是子进程的ID



### 线程

概念：

1. 线程是可由CPU直接运行的实体；
2. 一个进程可以创建多个线程；
3. 多个线程共享CPU，可以实现并发运行。

CreateThread（）把一个函数创建为一个线程

同时画圆画方问题。



#### **临界资源**Critical Resource

一次只允许一个进程独占访问（使用）的资源。如：共享变量，如果一旦改变，两个函数的结果就不同了



**临界区**（Critical Section）

进程中访问临界资源的程序段。



临界区和临界资源的访问特点

具有排他性

并发进程不能同时进入临界区。



设计临界区访问机制的四个原则

**忙则等待**：当临界区忙时，其他进程必须在临界区外等待。

**有限等待**，进程进入临界区的请求应该在有限时间内得到满足。



### 锁机制

基本原理

设置一个**标志**S：

​	表明临界资源“可用”还是“不可用”？1:0

进入临界区之前，检查标志是否可用？

​	若为“不可用”状态：进程在临界区之外等待

​    若为“可用”状态：

​		访问临界资源

​		且将标志修改为“不可用”

退开临界区时将标志重新修改回“可用”状态



#### 互斥和同步

进程的**互斥**：

没有两个或以上的进程同时进行存取操作。

互斥和资源共享相关

资源：临界资源

存取操作区域：临界区



**进程的同步**

合作进程为了完成一个共同任务，需要相互协调运行步骤

一个进程开始某个操作之前必须要求另外一个进程已经完成某个操作。否则前面的进程只能等待。

如：司机和售票员之间。

司机：起步（售票员关门后，否则等待），行驶，停车

售票员：关门，售票，开门（司机停车后，否则等待）



进程的某些操作间需要满足某种先后关系。



#### P-V操作

信号灯的概念：

进程在运行过程中受信号灯状态控制，并能改变信号灯状态。

​	进程受信号灯控制的：信号灯的状态，可以去阻塞或唤醒进程

​	改变信号灯：信号灯的状态，可以被进程改变



两个操作：

**P操作**（passeren通过）

S值减1；

若差大于或等于零，该进程继续；

若差小于零，则该进程**阻塞**并加入到队列q中，并转调度函数。

S初值很重要！



**V操作**（Vrijgeven释放）

S值加1；

如果和大于零，该进程继续；

如果和小于等于0，进程同时从q中**唤醒**一个进程。



**P-V操作解决互斥**

进入临界区之前执行P操作；（可能阻塞）

离开临界区之后执行V操作；（可能唤醒）

三个进程，在第一个进程调用时，S赋值为1.可以达到互斥



##### P-V操作解决同步

###### 生产者和消费者问题

 一群生产者向一群消费者提供产品，共享缓冲区。

![image-20200827221940482](MySQL.assets/image-20200827221940482.png)

两种情况：

① 生产者往缓冲区放东西放的太快，超过了缓冲区存放大小。就会出现数据覆盖。

② 消费者消费速度过快，消费完之后，还在缓冲区取数据。



因此有规则：

1. 不能向**满**缓冲区**存**产品
2. 不能从**空**缓冲区**取**产品（互斥）
3. 每个时刻仅允许**1个生产者或消费者存或取1个产品**。（互斥）



生产者生产1个数据：

P（empty）；//当empty小于0时，进程阻塞。

P（mutex）；//满足第三个条件的互斥操作

存1个数据到缓冲区

V（mutex）；

V（full）；



消费者消费1个数据：

P（full）；//当empty小于0时，进程阻塞。

P（mutex）；//满足第三个条件的互斥操作

从缓冲区取1个数据

V（mutex）；

V（empty）；



读者和编者问题

有一本书：读者读书，有多个读者；编者编书，有多个编者

要求：

允许多个读者同时读（不互斥）

不允许读者，编者同时操作（读者和编者之间的互斥）

不允许多个编者同时操作（编者之间互斥）



读者读书                                                               编者编书

**P(mutex);**

ReadCount++; //因为这个是临界资源，因此前面还需要加个锁

if(ReadCount==1)  //保证了除第一个读者需要判断编者是否编书外，其他读者可以顺利读书，互相是同步关系                                                     P(editor);

P(editor);     //和编者编书的PV操作 ，                    编书；

**V(mutex);**

读书             //形成了读者与编者的互斥                V(editor);

**P(mutex);**

ReadCount --;

if(ReadCount==0)

​	V(editor);

**V(mutex);**



### 进程通信（删）

#### windows 匿名管道通信

把CMD控制台程序，改成窗口程序

管道通信机制：

1. 管道定义：pipe

   管道是进程间的一种通信机制，一个进程A可以通过管道把数据传送给另一个进程B

2. 管道工作原理

   像文件一样，有读和写两个句柄。A写，B读

   仅能用于父子，或兄弟进程间通信。①父进程A创建管道；②父进程A创建子进程B；③父进程写或读管道，子进程输入或输出重定向到管道。

   

Linux信号通信

信号是Linux进程间一种重要的通信机制。

信号是向进程发送的一个通知，

收到信号的进程可以立刻执行操作

信号可以由进程发出，也可以由系统发出。



### 死锁

#### 哲学家就餐问题

 吃饭用两个筷子：拿一双筷子才能吃；每次只取一支筷子；只取身边的筷子；吃完放下筷子。

取左手边的筷子；P(S[i])

取右手边的筷子；P(S[i+4%5])

吃饭；

放下右手边筷子；V(S[i+4%5])

放下左手边筷子；P(S[i])

同时吃饭时，陷入死锁。

例子：小明：妈妈做饭我就回家；妈妈：小孩回家我就做饭。



死锁定义：两个或多个进程**无限期等待，永远不会发生**的条件的一种系统状态。【结果：每个进程永远阻塞】（我的理解是，进程的条件不合理，如小明和妈妈）（进程推进的顺序是非法的）

另一个定义：每个进程都持有**某种资源**，又继续申请其他进程已经持有的资源，因此每个进程都拥有其运行所需的一部分资源，**又不够**。（资源竞争，）



#### 死锁产生的条件

1. 互斥条件：一段时间内某资源仅为一个进程所占用
2. 请求-保持条件：当进程因为请求资源而阻塞时，对已获得的资源保持不放。
3. 不剥夺条件：进程获得的资源在没用完之前不能剥夺，只能使用完后由自己释放。
4. 环路等待：发生死锁时，必然存在一个进程-资源的环形链。



#### 预防死锁

资源一次性分配：一次性分配所有资源（破坏请求条件）

只要有一个资源得不到分配，不给这个进程分配其他资源（破坏保持条件）

可剥夺资源：当某进程获得了部分资源，得不到 其他资源，释放已占用的资源（破坏不可剥夺条件）



银行家算法

有很多钱，但是不够借给所有人。每个人借一点的话，所有人都不能得到满足。

概念：

安全序列：资源分配的先后顺序（

安全/不安全状态（不安全状态，不代表死锁，只是找不到一个方法分配资源）



数据结构

①. Allocation（资源占用情况）

②. Max（最大需求矩阵，每个进程最多需要多少资源）

③. Need（还需要多少资源）

Need = Max - Allocation

④. Available（ 系统中可用的资源数）



银行家算法：首先尝试分配，如果进入不安全状态，则不分配，否则分配。



### 进程调度

目标：

1 响应速度尽可能快；2 进程处理时间尽可能短；3 系统吞吐量尽可能大；4 资源利用率尽可能高；5 对所有进程要公平；6 避免饥饿

但它们是互相矛盾的。不可能都满足



典型调度方法：

1. **先来先服务**（First Come First Serve)，先进入系统，优先运行。

   但只考虑了**等候时间**，没考虑**运行时间**长短。

2. **短作业优先**（Short Job First），选取最短的作业投入运行。

   易于实现，效率不高。忽视了等待时间。容易饥饿。

3. **响应比高者优先调度**，响应比 = 响应时间/运行时间 = （等待时间+运行时间）/运行时间

   特点：①有利于短作业；②有利于等待长的作业。

4. 优先数调度，进程优先数 = 静态优先数+动态优先数。

5. 循环轮转调度（ROUND-ROBIN），按照先进先出排成队列，以时间片q为单位，轮流使用CPU。运行完一个时间片的进程排到队列末尾，等候下一轮运行。

   公平、交互（等待(N-1)*q就可以重新获得）



### 内存管理

存储管理的功能：地址映射、虚拟存储、内存分配和存储保护。

1）地址映射：把程序中的地址（虚拟地址/虚地址/逻辑地址）转化成内存的真实地址（实地址/物理地址）的过程。

2）方式：固定地址映射、静态地址映射、逻辑地址映射



分区存储管理

​	单一存储管理

​	分区存储管理：

​		**固定分区**  （分区大小和位置固定，系统运行期间不再重新划分），分区表（记录分区位置、大小和使用标志）![image-20200828144540940](MySQL.assets\image-20200828144540940.png)

​		缺点：浪费内存（程序比内存小），大程序可能无法运行：比最大分区大。		

​		

​		**动态分区**  （程序装入时创建分区），分区动态建立。

​		问题：内存碎片过多。有过小的空闲区，难以实际利用。

​		建立空闲区表：		![image-20200828145328179](MySQL.assets\image-20200828145328179.png)

​		分割空闲区时，一般**从底部分割**。

​		回收时，需要合并，更新大小。



最佳适应法，最容易产生内存碎片。因为找到的分区和用户需求最接近。

改善物理内存管理的技术：内存拼接、覆盖技术（相同层次的程序，共享内存）



#### 虚拟内存管理

程序运行时，只把当前必要的一小部分装入内存。

其余的等需要时再装入。不运行时及时删除。

局部性：

有限时间内，在有限的地址范围内。



##### 页式虚拟内存管理

进程空间和内存空间划分成等大小的小片



虚拟地址（VA）可以被分解成页号P和页内偏移W。

页号P：   VA所处页的编号 = VA/页的大小         页号 = VA>>n

页内偏移W： 偏移= VA%页的大小                    页内偏移W = 低n位 = VA&&(2^n-1)



**页面映射表**

哪一页放入了哪个页框中。![image-20200828161027708](MySQL.assets\image-20200828161027708.png)

已知虚拟地址，求物理地址：

① 从虚拟地址，得到**页号和页内偏移**；

② 查询页表，找到**页框号**；

③ 物理地址MA = 页框号 × 页大小 + W。



如：已知页式地址，2500，页面大小1024，得到页号是2，偏移是452；查到页框号是6，那么物理地址为：

6×1024 + 452 = 6596



##### **快表机制（Cache）**

慢表：页表放在内存里

快表：Cache里。部分慢表内容。地址映射时先访问快表，如果快表中不存在，则去内存中寻找，并且把这个映射关系保存在快表中。





#### 缺页中断

Cache和内存中都没有找到该页，需要去辅存中寻找，叫缺页，会发生缺页中断。

页表扩充： 添加**中断位**和**辅存地址**的页表。     中断位 = 1，不在内存。

​					带访问位和修改位的页表。			访问位：标识页表是否最近被访问？ 修改位：该页数据是否已被修改？

缺页中断处理：

中断处理程序，把所缺的页从页表指向的辅存地址，调入内存的某个页框中 （如果没有空闲页，**淘汰**一个页，将淘汰页放入辅存）。

更新该页对应的页框号，修改中断位为0。



##### 页面淘汰

最佳算法（OPT算法），淘汰后不再需要，或很久才会用到的。

​		无法实现，因为无法知道将来会不会使用。



先进先出（FIFO算法）淘汰在内存中停留时间最长的页面。    缺页率很高。



最久未使用淘汰算法（LRU，least recently used），淘汰最长时间未被使用的页面。



逐行搜索，比逐列搜索，速度更快。因为数组存储时按行存储，如果按列搜索，容易出现缺页。



段式地址映射

将程序分成段，在内存中存储。









# java

## java基本语法

标识符 与命名

关键字和保留字
关键字（keyword）的定义和特点
定义：被java语言赋予了特殊含义，用作专门用途的字符（字符串）
关键字中所有字母都是小写
比如class、interface、if、long、else等
true、false、null比较特殊，它们用于定义数据类型值的，当作关键字

保留字
java保留字：现有java版本上位使用，以后可能会作为关键字使用。
goto、const

2. 标识符
java对各种变量、方法和类等要素命名时使用的字符序列称为标识符
（与c很像）
由26个英文字母大小写，0-9，_或$组成
数字不可以开头
不可以使用关键字和保留字，但是可以包含关键字和保留字。
java严格区分大小写，长度无限制。
但标识符不能包含空格。

3. java的名称命名规范（最好这样做）
包含：多单词组成时，所有字母都小写：xxyyz
类名、接口明：多单词组成时，所有单词的首字母大写：XxxYyy
变量名、方法名：多单词组成时，第一个单词首字母小写，第二个单词开始每个单词首字母大写：xxYyyZzz
常量名：所有字母都大写，多单词时每个单词用下划线连接：XXX_YYY

起名时，为了提高阅读性，尽量有意义“见名知意”
中文可以当标识符，但是不要这样写

#### 变量

变量
变量的概念：
内存中的一个存储区域
区域数据可以再同一类型范围内不断变化
是程序中最基本的存储单元，包含变量类型、变量名和存储值
作用是在内存中保存数据

java的每个变量必须先声明，后使用，这与c是一致的
变量都定义在其作用域内，出了作用域就失效了
同一个作用域内，不可以生命两个同名变量

java变量分类：按照数据类型
基本数据类型分为：

1. 数值型
整数类型（byte，short，int，long）
浮点类型（float，double）

2. 字符型（char）

3. 布尔型（boolean）


引用数据类型
类（class）字符串是一个类的类型

接口（interface）

数组（array）

二、根据变量在类中的位置，分为成员变量 和 局部变量


1. 整数类型：byte、short、int、long
byte 1字节=8bit位 表示256个数 -128~127
short 2字节       -2^15~2^15-1
int   4字节       约21亿
long  8字节

声明long型的变量时，必须以“l”或者“L”结尾
如 long l1 = 2343251154543L;
通常，定义整型变量时，定义为int类型

2. 浮点类型：float、double
float  单精度 4字节 -3.403E38~3.403E38
double 双精度 8字节 -1.798E308~1.798E308
定义float类型的变量时，变量以“f”或者“F”结尾
如： float f1 = 12.3F
通常使用时，用double类型

3. 字符类型：char
    char 1字符=2字节
    定义char型变量，通常用一对‘ ’

  但是单引号中一定要放东西。

'\t'代表制表符

'\u'开头的表示字符的编码，如'\u0123'是一个中文字符。

想把特殊字符也显示出来，就在前面再加个\

如'\\\u'输出的就是\u

4. boolean型

   只能取两个值：true或者false



在java8中，带小数点的数默认为double类型，无法赋值给float。



String**不是**基本的数据类型！

String属于引用数据类型。

声明string类型变量时，初始化用一对““

string可以和8种基本数据类型变量做运算，且运算只能是连接运算。

如：

int number = 1001;

String numberStr = "学号"；

bool b1 = true;

String info = numberStr + number+true;

System.out.println(info);

这时输出的是：学号：1001true



需要注意：只有加号两边都是字符串，+才表示连接

如果字符串两边是' ' 一个char类型，它保存实际是ascii码。所以+表示ascii码相加。



运算符注意点：

&代表与运算，不管前后结果是真是假，都要运算

&&代表且，如果前面为假，后面就不运算了

|运算，不管前后结果是真是假，都要运算

||运算，只要前面为真，后面就不运算了



#### 位运算符

<<左移 3<<2=12:  3\*2\*2=12

\>>右移 3>>1 = 1  3/2=1

\>>>无符号右移，被移位的最高位无论是0是1，空缺位都补0。

&、|、^(异或)、~非



三元运算符：

结构：（条件表达式）？表达式1：表达式2

条件表达式的结果为boolean类型

根据条件表达式真或假，决定执行表达式1，还是表达式2

如果表达式为true，执行表达式1

表达式为false，执行表达式2

表达式1和表达式2需要是一个类型的。



#### 流程控制

顺序结构

分支结构  if……else……   switch（）：case

循环结构



从键盘获取不同类型的变量，需要使用Scanner类。

1. 导包：import java.util.Scanner;

2. Scanner 的实例化（创建一个scanner的对象） 

   ```
   Scanner scan = new Scanner(System.in);
   int score = scan.nextInt();
   ```



多个条件表达式之间是“互斥”关系（没有交集），判断语句在上面下面没有关系。

如果多个条件表达式之间有交集，就有影响。

```
switch（表达式）{
case 常量1：
	语句1；

//break；
case 常量2：
	语句2；
//break；
default：
	语句；
//break；
}
```



#### 数组

数组是引用数据类型。

int[] id;

id = new int[]{0,0,0,0};  或者 id = new int[5]; //动态初始化，只告诉数组长度，不初始化值。

String[] names = new String[5];

数组元素的默认初始化值



在JVM中，有**栈**（stack）存放局部变量、**堆**（heap）存放new出来的结构：对象，数组

方法区（函数），有**常量池**和**静态域**



一维数组的内存解析

如，int[] arr = new int[]{1,2,3}; 此时读到等式左边，int arr，java在**栈**中创建一个arr

然后右边 new int[]，java在**堆**中，创建了一个长度为3的数组，并把它的初始值都赋为0.

然后读取{1,2,3}，java把初始值0，替换为1,2,3.并把数组的**首地址**，放到栈的arr下面。

每次**new**一个对象、数组，都会在堆里开辟一个**新的地址，并且初始化**。



二维数组的内存解析 P153

行数是必须赋值的，列数可以不赋值。

String\[][]  arr4 = new String\[3][];      ->在栈中创建一个arr4，在堆中创建行数为3的一个String，存放指针。并把String的地址赋值给arr4.

arr4[1] = new String[4];   ->在堆中另外创建一个长度为4的String，并把首地址赋值给堆中String的第一行位置。



跟C语言的二维数组不同，java的每一行，都可以是变长的。

在动态初始化之后，int\[][]  arr =  new int\[2][];   可以对arr每一维进行初始化。如：arr[0] =new int[5];  意思代表，把new int[5]的地址值赋给arr



System.out.println(arr4[0])     // [I@16f65612，[代表一维数组，I代表int型

System.out.println(arr4)        // [[I@6d01d69c，[[代表二维数组，I代表int型

（引用类型的变量，存储的实际上是地址值）

 

import java.util.Arrays
数组的工具包，含有函数：

1. boolean equals(int[] a,int[] b);       判断两个数组**是否相等**，返回boolean类型。
2. String toString(int[] a);                输出数组信息。（以String的形式）
3. void fill(int[] a,int val);                  将指定值**填充**到数组中（填充同一个值）
4. void sort(int[] a);                           对数组进行**排序**

5. int binarySearch(int[] a,int key) ;      二分查找，找到返回索引，没找到返回负数

   // int index = Arrays.binarySearch(arr,10);



#### 常见异常

1. 数组角标越界：ArrayIndexOutOfBoundsExcetion

2. 空指针异常：NullPointerException             这个变量里面存放的是指针，但指针指向一个null



## 面向对象

1. java类及类的成员：属性、方法、构造器；代码块、内部类
2. 面向对象三大特征：封装性、继承性、多态性
3. 其他关键字：this、super、static、final、abstract、interface、package……



面向过程：强调功能行为，以函数为单位，考虑怎么做

面向对象：具备了功能的对象，以类/对象为最小单位，考虑谁来做



对象：包含属性（成员变量）和方法（函数）。

通过“对象.属性”或“对象.方法”调用对象的结构。



#### 内存解析

![image-20200830092340939](MySQL.assets\image-20200830092340939.png)

**堆**（Heap），此内存区域，**存放对象实例**，几乎所有对象实例都可以在这分配内存。

**栈**（Stack），虚拟机栈，**存储局部变量**（方法中的变量，都是局部变量），包括基本数据类型（boolean、byte、char、short、int、float、long、double），对象引用，它不是对象本身，而是对象在堆内存放的首地址。

**方法区**（Method Area），存储**类信息、常量池、静态变量、即时编译器编译后的代码**。



成员变量：

直接定义在类的一对{}内

权限修饰符：private、public、protected

局部变量：		

声明在方法内、方法形参、代码块内、构造器形参、构造器内部的变量。

它们在内存中加载的位置：

成员变量：加载到堆空间中（非static）

局部变量：加载到栈空间中



一个函数的调用过程：

函数内的都是局部变量，因此放在栈空间中，创建了新的对象，放在堆空间中。指向地址在栈空间。

当函数调用结束，栈空间元素一个个被pop出来。JVM的垃圾回收机制，会将堆空间中，没有指针指向的那些对象回收掉。



匿名对象：

只调用一次。

new 对象.属性；或者   new 对象.方法； 



#### **方法的重载**

两同一不同：同一个类、相同函数名

​						参数列表不同、参数个数不同、参数类型不同

如想要进行排序，可以对int类型排序，是一个函数。

对char类型排序，又是一个函数。对float……



这时因为函数名相同，在调用时，如何指定是哪一个函数？

①函数名确定，②参数列表（可以自动类型提升）



可变个数形参。

如：

public void show(String ...str){

}

代表，可以传入很多个形参，形参类型都是String。但这种可变形参，必须放在最后。这也代表，可变个数形参，每个函数最多只能一个。



封装性：降低耦合。

封装性的体现：①不对外暴露的私有方法  ② 单例模式……

权限修饰符：四种权限可见性的大小，从小到大：private、不填、protested、public

![image-20200830162156123](MySQL.assets/image-20200830162156123.png)



4种权限可以修饰类及类的内部结构：属性、方法、构造器、内部类。

修饰类的，只能用**public**和**不填**。



#### 构造器

任何一个类都有构造器（constructor）。它的作用是创建对象、初始化对象。

语法是：public class_name() {}

构造器权限默认和类权限是相同的。

如果没有定义时，系统默认提供一个空参的构造器。 但是一旦我们定义了一个类的构造器后，系统不再提供默认的空参构造器。

一个类中定义多个构造器，彼此构成重载。



它与set函数不同，设置set函数，表明把形参传递的值赋给对象。



属性赋值的方式：

① 默认初始化 		② 显式初始化（类中初始化）		③ 构造器中赋值		④ 通过“对象.方法”或"对象.属性"的方式赋值



**JavaBean**

JavaBean，符合以下标准的java类： ① 类是公共的；② 有一个无参的公共的构造器； ③ 有属性，且有对应的get、set方法



UML类图

![image-20200830184836289](MySQL.assets/image-20200830184836289.png)



**this调用属性和方法**

为什么需要调用this？

在建造构造器、或者set函数时，传入的形参，通常与类中的属性名相同。

如：

public class Person{

​	int age;

​	public  Person(int age){

​		this.age = age;

}

}

this意为：当前对象。

**this调用构造器**

1. 在类的构造器中，可以使用this(形参列表)，调用本类中**其他**构造器。

2. this（形参列表）必须声明在首行。

3. 一个构造器中，最多只能调用一个本类的其他构造器。但构造器不能形成环（否则就出不去了）

如：

```
public class Person{
	int age;
	public  Person(){
		
	}
	public  Person(int age){
		this();      //此处调用的是Person()构造器。
		this.age = age;
	}
}
```



快捷方式，构造set、get函数：

source-generate getters and setters

构造器：source-generate constructor using fields



#### package 关键字的使用

1. 为了实现项目中类的管理，出现了包

2. 使用package声明类或接口所述的包，出现在首行
3. 包，命名小写，见名知意
4. 每 “ . ” 一次，代表一层文件目录。

同一个包下，不能命名通名的接口、类



**import关键字的使用**

import：导入

导入指定包下的类、接口

“xxx.*"的形式，表示导入xxx包下的所有结构。

如果使用的类或接口是java.lang包下定义的，则可以省略import结构。

当导入不同的包里，有重名的一个类。那么在调用时，需要将包 . 类的方式调用（全类名）。

import static：导入指定类或接口中的静态结构：属性或方法。



#### MVC设计模式

常用的设计模式之一，将程序分为是哪个层次：视图模型层，控制器层与数据模型层。

这种将程序输入输出、数据处理以及数据的展示分离开的设计模式。使程序结构变得灵活而且清晰。也描述了程序各个对象间的通信方式，降低了程序的耦合性。

![image-20200830214234922](MySQL.assets/image-20200830214234922.png)

模型层 model 主要处理数据     

\>数据对象封装 model.bean/domain

\>数据库操作类 model.dao

\>数据库             model.db



控制层 controller 处理业务逻辑   

\>应用界面相关   controller.activity

\>存放fragment  controller.fragment

![image-20200830214748526](MySQL.assets/image-20200830214748526.png)

根据功能，去定位包。



封装性的体现：

一、类的属性私有化

二、 方法（函数）私有化

三、 单例模式（构造器私有化）

四、 如果不希望类在包外被调用，可以将类设置为缺省的。



**eclipse 常用快捷键**

1. 补全代码：alt+/   非常好用！！
2. 快速修复：ctril + 1
3.  批量导包： 
4. 多行注释：ctrl + /
28. 大写小写转换：变成大写：ctrl+shift+x，变成小写：ctrl+shift+y



### 继承性

![image-20200831203458871](MySQL.assets/image-20200831203458871.png)

子类可以继承父类的属性、函数等。

只需要在类名后面， 加上 extend father_name

继承性的**好处**：

1. 减少代码冗余，提高代码复用性
2. 便于功能扩展（子类都想加一个功能，这时只需要在父类中添加即可）
3. 为之后多态性的使用，提供前提



继承性的**格式**：class A extends B{}

A：子类、派生类、subclass

B：父类、超类、基类、superclass



体现：

1. 一旦子类A继承父类B以后，子类A中就获取了父类B中声明的**所有**的属性和方法。

2. 子类继承父类后，子类还可以声明自己特有的属性或方法，实现功能的拓展。



Java中关于继承性的**规定**：

1. 一个类可以被多个类继承
2. 类的单继承性：一个类只能有一个父类
3. 子父类是相对的，可以有多层继承，如一个父类A有一个子类B，这个子类B，还可以有另外一个子类C。（父类A的孙子C）这时，B对于C来说，是C的父类。
4. 子类C不仅包含父类B的属性和方法，还拥有间接父类A的所有属性和方法。



不同包的子类，可以调用另外一个包中父类的protected方法、属性。



#### 方法的重写（覆盖）

子类继承父类后，可以对父类中同名，同参数的方法，进行覆盖操作。

快捷键：alt+/

重写以后，当创建子类对象后，调用此同名同参数的方法时，实际执行的是，在子类中重写的方法。

重写的**规定**：

方法的声明： 权限修饰符 返回值类型 方法名（形参列表）throws 异常的类型{

}

1. 子类的叫重写的方法，父类中的叫被重写的方法。

2. 子类重写的方法的方法名和形参列表，与父类被重写的方法名和参数列表  相同

3. 子类重写的方法，权限大于等于父类被重写的方法。

   > 特殊情况：子类**不能**重写 **private**的父类的方法。

4. 返回值类型：

   > 父类返回值类型是 void，子类的也只能是void
   >
   > 父类返回值类型是A类型，子类的返回值类型，可以是A类，也可以是A类的子类。
   >
   > 父类的返回值类型，是基本数据类型，子类也必须是相同的返回类型（**不能**自动类型提升！！）。

5. 子类重写的方法，抛出的异常类型，不大于父类被重写的方法，抛出的异常类型



要么都是static，要么都不是static

但是只有非static 类型的，才叫重写。



#### Super关键字

super代表父类的。与this有相似之处。

子类调用父类的属性

如果子类父类有相同的属性，可以通过super. 属性来调用父类的属性。

super调用构造器。

1. 可以用super(形参列表)，给父类的构造器赋值，来创建父类。

3. super（形参列表）的使用，必须声明在子类构造器的首行。

   那么这就代表，super和this调用构造器时，只能选择一个。

4. 在构造器首行，没有显式地声明super和this，默认调用super（）

   

   这就代表，如果构造父类时，最好写一个**空构造器**！！

   

5. 在类的多个构造器中，至少有一个构造器中，使用了super（形参列表），调用父类的构造器。



**子类对象实例化过程**

1. 子类继承父类后，获取了父类声明的所有属性和方法。

   因此创建子类对象，在堆空间中，会加在所有父类中声明的属性。

2. 通过子类的构造器创造子类对象时，一定会直接或间接调用父类构造器……

   直到调用java.lang.Object类中空参的构造器为止。

   因此内存中有父类的结构，子类才可以调用父类的属性和结构。

   但是，父类的对象**并不是new出来的**，而是继承了父类的属性。



因此，对封装性又有了新的理解……

所谓封装，是把属性的地址隐藏起来，只有类内部才能够知道。

对外只保留了类的地址。只可以通过类的函数，来调用类内属性的地址。

#### Object类

1. 如果没有显示地声明，这个类包含父类，则此类继承与java.lang.Object类
2. 所有的类，都直接或间接地继承于 java.lang.Object类



1. Object类是所有java类的根父类。

2. 如果在类声明中未使用extends关键字，指明它的父类。默认父类是java.lang.Object类。
3. Object类 中的功能（属性、方法）具有通用性。
4. Object类只声明了一个空参的构造器。 



Object类中有finalize方法，它是JVM中自动的垃圾回收机制，在回收前会调用的一个函数

equals（）/toString（）



equals(Object obj) 方法和 == 的区别

1. == 是运算符。

   使用在基本数据类型变量，和引用数据类型变量中。

   基本数据类型变量，比较的是数据是否相同

   引用数据类型，比较是地址是否相同。



2. Object类中equals() 的定义：

   Object类中，定义的equals，和 == 相等。



3. String、Date、File、包装类等，都重写了Object类中的 equals() 方法

   因为这些类中，实际上比较的是实体内容。



4. 自定义的类，如果使用equals() 的话，也通常比较实体内容。

   因此需要重写。

   重写：（根据String类型来重写）

   ①先判断地址是否相同？相同直接return true；

   ② 判断是否是同一个类型？是的，再比较每个属性。

还可以自动生成 - source中



### 多态性

理解多态性：

一个事务的多种形态。

声明的类型，与new 的类型可以不同。**父类的引用指向子类的对象**。

如，父类person，子类man，woman。

person p1 = new man();

**此时，调用子类和父类同名，同参数的方法时，实际执行的是子类重写父类的方法。**

**但是，不能调用子类中有，父类中没有的方法。**



多态的使用。

有了对象的多态性后，在编译期，只能调用父类中声明的方法。虚拟方法调用

运行期，实际执行的是子类重写父类的**方法**。

编译：看左边； 	运行：看右边。

因为多态，是**运行时行为**。



多态的使用前提：

① 类的继承关系。

② 方法的重写。



**但属性没有多态性**。

属性只跟右边声明的类型有关。



#### 向下转型

使用强制类型转换符。![image-20200901112006239](MySQL.assets\image-20200901112006239.png)

如：

Person父类，man子类，woman子类。

Person p1 = new man();

man p2 = (man) p1;



使用强转时，可能出现 **ClassCastException**的异常。

因为能够强转，是因为new的时候，用了多态性。将子类对象，赋给了父类的指针。



**instanceof关键字**

a instanceof A：判断对象a是否是类A的实例。

如果是，返回true，如果不是，返回false。



如果 a instanceof A 返回了 true。B是A的父类。

那么 a instanceof B 也一定返回true。



**toString()**

1. 当我们用toString（）输出一个对象的引用时，实际上就是调用当前对象的toString();

2. Object类中toString() 的定义：

   return getClass().getName()+"@"+Interger.toHexString(hashCode());

3. String、Date、File、包装类等，都重写了Object类中的toString（）方法。

4. 自定义类也可以重写toString（）方法。



### 包装类

针对八种基本数据类型，定义相应的引用类型：包装类（封装类）

就可以调用类中的方法。

![image-20200901193124664](MySQL.assets/image-20200901193124664.png)



8中基本数据类型，对应的包装类，使得基本数据类型的变量具有类的特征。



包装类--->转换成基本数据类型：调用包装类Xxx的xxValue()
如：

Integer in1 = new Integer(12);

int i = in1.intValue();



基本数据类型--->转换成包装类，可以直接new，甚至，可以把它作为一个字符串，传入形参。

如：

i = 10;

Integer in1 = new Integer(i);

integer in2 = new Integer("10");

in1.equals(in2);   //返回的结果是true



自动装箱与拆箱：

基本数据类型--->包装类

int num2 = 10;

Integer in1 = num2;   //自动装箱



自动装箱，实际上是Integer内部，定义了IntegerCache结构。

保存了 -128 ~ 127范围的整数，只要赋值范围在 -128~127范围内时，就可以使用数组中的元素，不用new。

超出范围的，就是new了一个对象。





自动拆箱：包装类--->基本数据类型

int num3 = in1;     //自动拆箱。



**基本数据类型与String类的相互转换**

方式一：

```
//自动连接
int num = 10;
String str = num + "";
```



方式二：

调用String 重载的valueOf（Xxx xxx）

```
float f1 = 12.3f;
String str = String.valueOf(f1);    //"12.3"

double d1 = new Double(12.3);
String str2 = String.valueOf(d1);   //自动拆箱，并转换成String"12.3"
```



String类型--->转换成基本数据类型

调用**parseXxx**(String s)

如：

```
String str = "123";
int num = Integer.parseInt(str);
float f = Float.parseFloat(str);

String str2 = "true";
boolean b1 = Boolean.parseBoolean(str2);
```

boolean型比较特殊，只要不是true，都是false。

如：“true1”，也是false



注意：类型转换时，由于不匹配，如，要将“123abc”转换成int型

可能出现异常：NumberFormatException



对多态性的理解？

多态：是为了实现代码的通用性。

抽象类、接口的使用，肯定体现了多态性（抽象类、接口不能实例化）





#### Static关键字

1. static：静态的

2. static可以用来修饰：属性、方法、代码块、内部类

3. 使用static修饰属性：静态变量（类变量）。

   属性：按照是否使用static修饰，分为静态属性 vs 非静态属性（实例变量）

   

   静态变量：多个共享共享一个静态变量。当一个对象修改静态变量时，导致其他对象调用此静态变量时，是修改后的。

   静态变量，随着类的加载而加载。可以通过“类名.静态变量"的方式进行调用

   静态变量的加载早于对象的创建。

   由于类只会加载一次，静态变量在内存中也只会存在一份，存在**方法区的静态域**中。

   

   静态属性举例：System.out、Math.PI

   

4. 使用static修饰方法：静态方法

   ① 随着类的加载而加载，可以通过“类名.静态方法”来调用。

   ②静态方法中，只能调用静态方法和静态属性。 

   ​	非静态方法中，既可以调用静态方法或属性， 也可以调用非静态~~。



开发中，如何确定一个属性是否要声明为static？

​		所以对象共用一个的。不会随着对象不同而不同 。



方法声明为static？

​		操作静态属性的方法， 通常设置为 static的。

​		工具类中的方法：Math，Arrays（不专门为某个对象服务。而是所有对象都能调用）



### 单例（Singleton）设计模式

设计模式是解决问题的一种思考方式。

创建型模式，5种：工厂方法模式、抽象工厂模式、单例模式、建造者模式、原型模式。

结构型模式，7种：适配器模式、装饰器模式、代理模式、外观模式、桥接模式、组合模式、享元模式

行为型模式，11中：策略模式……命令模式、备忘录模式、状态模式、访问者模式……



单例设计模式：

保证在整个软件系统中，对某类，**只能存在一个对象实例**。且该类只能提供一个取得其对象实例的方法。

首先，必须将类的构造器的访问权限设置为private。

**类内部**可以**产生**该类的对象。

类外部只能调用该类的某个静态方法，返回内部创建的对象。



实现:

#### **//饿汉式**

```
class bank{
//1. 私有化类的构造器
private Bank(){
}

//2. 内部创建类的对象
//4. 要求此对象也必须声明为静态的（这样才能够在静态方法中调用）（静态方法只能调用静态方法或静态变量）
private static Bank instance = new Bank();

//3. 提供公共的静态的方法，返回类的对象
public static Bank getInstance(){
	return instance;
}
}
```



**//懒汉式**

```
class Order{
	//1. 私有化类的构造器
	private Order(){
		
	}
	//2. 声明当前类对象，没有初始化
	//4. 此对象也必须声明为static
	private static Order instance = null;
	
	//3. 声明public、static的返回当前类对象的方法
	public static Order getInstance(){
		if(instance == null){
			instance = new Order();
		}
		return instance;
	}
}
```



饿汉式：

​			坏处：对象加载时间过长，占用内存

​			好处：线程安全的。

懒汉式：

​			好处：延迟对象的创建。

​			坏处：目前，线程不安全。



网站的计数器、应用程序的日志应用、数据库连接池

Application也是单例的典型应用

任务管理器、回收站，也是典型的单例应用。



main()方法的使用说明：

1. main()方法作为程序的入口
2. main()方法也是一个普通的静态方法
3. 调用的只能是静态方法，或静态变量，或者通过对象去调用方法。
4. main()方法，也可以作为我们与控制台交互的方式



### 代码块

一段{

}大括号里的内容。

1. 代码块的作用：用来初始化类、对象
2. 代码块如果有修饰的话，只能使用static
3. 静态代码块 vs 非静态代码块
4. 静态代码块

```
static{
	System.out.println("hello java world");
}
```

> 随着类的加载而执行。（调用）

> 只执行加载类的这一次。

> 初始化类的信息，如静态变量初始化。



5. 非静态代码块

```
static{
	System.out.println("hello java world");
}
```

> 随着对象的创建，而执行。（调用）

> 每创建一个对象，就执行一次非静态代码块

> 作用：在创建对象是，对对象的属性等进行初始化。
>
> 非静态代码块的执行，先于构造器的执行。



#### final关键字

final：最终的

1. final可以用来修饰的结构：类、方法、变量

2.  final  用来修饰一个类：不能再有儿子了。（不能有子类了）

   ​					比如：String类、System类、StringBuffer类

3. final  用来修饰方法：此方法不可以被重写。

   ​					比如：Object类中 getClass() 方法

4. final  用来修饰变量：此时的“变量”就成为是一个常量。

   ​					final 修饰属性：在对象new出来的时候，final修饰的属性就必须有确定			的值。

   ·					final 修饰局部变量：一旦确定了值，在方法内就不能更改。



static final 用来修饰属性：全局常量。



#### 抽象类与抽象方法

父类一般设计得非常抽象，子类越来越具体。

如：父类是动物，子类分成了海豚科、猫科……猫科又分为豹子、狸花猫……



abstract关键字的使用

1. 可以用来修饰的结构：类、方法

2. abstract修饰类

   \>此类不能实例化（不能new出来）

   \>抽象类中一定有构造器。子类对象实例化时调用。

   

3. abstract 修饰方法：抽象方法

   \>抽象方法，只有方法的声明，没有方法体。如： public abstract void eat();

   \>抽象方法，只能存在在抽象类中

   \>如果子类不是抽象类，则需要重写父类**所有的**抽象方法。



4. abstract 不能用来修饰私有方法、静态方法、final的方法、final的类。这三种方法都不能被重写，而abstract必须重写。



#### 模板方法设计模式（TemplateMethod）

功能内部一部分实现是确定的，一部分实现是不确定的，这是可以把不确定的部分暴露出去，让子类去实现。

不确定的部分可以设置为抽象方法。



### 接口

有时必须从几个类中派生出一个子类。

有时必须从几个类中，抽取一些共同的行为特征。

接口，与类，是平行的，同等的一个阶级。



接口的使用

1. 接口 使用 interface 来定义

2. Java中，接口和类是并列的两个结构

3. 如何定义接口，定义接口中的成员

   ​	JDK7及以前：只能定义全局常量和抽象方法

   ​							\>全局常量： public static final的，书写时可以省略不写。

   ​							\>抽象方法：public abstract的

   ​	JDK8 ：除了全局常量和抽象方法外，还可以定义静态方法和默认方法（default）

   ​					\>接口中的静态方法，只能通过接口来调用。

   

4. 接口中，不能定义构造器的！意味着接口不可以实例化。

5. Java开发中，接口通过让类去实现的方式来使用。

   ​			类如果想实现（implements）接口，则必须重写接口中所有的抽象方法！！

如：

```
interface Fly{
	public abstract void fly();
}
class Plane implements Fly{
	@Override      //重写了接口中的抽象方法
	public void fly(){
	}
}
```

6. 接口可以多继承：弥补了Java的单继承性的局限性。

语法： class AA extends BB implements CC,DD,EE

```
interface Fly{
	public abstract void fly();
}
interface Attack{
}
class Bullet extends Plane implements Fly,Attack{
	@Override      //重写了接口中的抽象方法
	public void fly(){
	}
}
```

7. 接口与接口之间，可以多继承。

   子接口，继承了父接口所有的抽象方法。



接口的使用上，也体现了多态性。

如：函数调用的形参，是一个接口。

但是接口不可能被new出来（它没有构造器），只能通过它实现的一个类来进行调用。

因此最后传入形参的，是实现接口的那个类，new出来的对象。



应用：数据库的接口

JDBC，都是面向接口编程。因为每个数据库软件的具体操作不一样。

创建接口匿名实现类的匿名对象：P354



Tips：

父类和实现的接口中有同名同参数的方法。

优先调用的是父类的方法——类优先



### 代理模式（Proxy）

代理设计时为其他对象，提供一种代理，以控制对这个对象的访问。

P355

被代理类，不对某个属性做详细操作。

代理类，对被代理类的某个属性/方法做详细操作。



类的内部成员：

#### 内部类

一个事务的内部，有一个部分需要完整的结构。

可以创建一个类，但是这个类只为本事务服务。就创建内部类。

1. Java 允许将一个类A声明在另外一个类B中。
2. 局部内部类（代码块、构造器、方法内） vs 成员内部类（静态、非静态）



### 工厂模式

实现创建者和调用者的分离。将创建对象的具体过程屏蔽起来。



## 异常处理

在开发过程中，有些问题不是靠代码能够避免的，如：客户输入数据的格式、读取文件是否存在、网络是否始终保持通畅。

Java程序发生的异常事件有两类：

1. **Error**：Java虚拟机无法解决的严重问题。如：JVM系统内部错误、资源耗尽等严重情况。比如：StackOverflowError和OOM。

2. **Exception**：其他因编程错误或偶然的外在因素导致的一般性问题。

   可以使用针对的代码进行处理，如：

   ① 空指针访问

   ② 试图读取不存在的文件

   ③ 网络连接中断

   ④ 数组角标越界



对于这些错误，程序员最好提前考虑到错误的检测、错误消息的提示，以及错误的处理。



![image-20200903101146346](MySQL.assets/image-20200903101146346.png)



一、异常体系结构

java.lang.Throwable

​		|----------java.lang.Error--------：一般不编写针对性的代码进行处理。

​		|----------java.lang.Exception----------：可以进行异常的处理

​				|----------编译时异常（checked）/受检异常

​								|----------IOException

​								|----------ClassNotFoundException

​				|----------运行时异常（unchecked）

​	

Java异常处理

采用的异常处理机制，将可能出现异常处理的程序集中在一起，与正常的程序分开。

方式一：

​			try-catch-finally

方式二：

​			throws + 异常类型



异常处理机制一：抓抛模型

过程一：“抛”，程序在正常执行过程中，一旦出现异常，就会在异常代码处，生成一个对应异常类的对象。

​						并将此对象抛出。

​						一旦抛出对象以后，其后的代码就不再执行。



过程二：“抓”，异常的处理方式：① try-catch-finally   ② throws



try-catch-finally 的使用



```

```



















# 项目调试

bug调试：

必要时添加输出语句

可以用Debug



### 单元测试

java中的JUnit单元测试

步骤：

1. 当前工程 - 右键选择： build path - add libraries - JUnit（next）- finish

2. 创建java类，进行单元测试

   此时的Java类要求：

   ①此类是公共的

   ②此类提供公共的无参的构造器。

   在类前、方法前，方法内  多添加必要的注释

3. 此类中声明单元测试方法：

   方法权限是public，没有返回值，没有形参

4. 此单元测试方法上，需要声明@Test，并在单元测试类中导入import org.junit.Test;

5. 声明好，可以在方法体内测试。

   左键双击单元测试方法名，右键：run as - JUnit Test

   也就是，写哪个单元，就可以测哪个单元。