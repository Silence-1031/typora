## 进阶篇

### 一、存储引擎

#### 1.MySQL体系结构

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-03 161642.png)

##### 1.1 连接层

    最上层是一些客户端和链接服务，主要完成一些类似于连接处理、授权认证、及相关的安全方案。服务器也会为安全接入的每个客户端验证它所具有的操作权限。

##### 1.2 服务层

    第二层架构主要完成大多数的核心服务功能，如SQL接口，并完成缓存的查询，SQL的分析和优化，部分内置函数的执行。所有跨存储引擎的功能也在这一层实现，如过程、函数等。

##### 1.3 引擎层

    存储引擎真正的负责了MySQL中数据的存储和提取，服务器通过API和存储引擎进行通信。不同的存储引擎具有不同的功能，这样我们可以根据自己的需要，来选取合适的存储引擎。

##### 1.4 存储层

    主要是将数据存储在文件系统之上，并完成与存储引擎的交互。



#### 2. 存储引擎

- 存储引擎就是存储数据，建立索引，更新/查询数据等技术的实现方式。存储引擎是基于表的，而不是基于数据库的，所以存储引擎也称**表类型**。
- 不同存储引擎存储的机制是不同的，在一个数据库下的多张表，可以选择不同的存储引擎

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-03 162301.png)

- mysql 5.5版本后，默认选择**InnoDB**存储引擎

##### 2.1 指定存储引擎

```sql
create table 表名(
		字段1 字段1类型 （comment 注释），
    	···
    	字段2 字段2类型 （comment 注释）
)engine = innodb (comment 注释);

-- eg:
create table my_myisam(
    id int,
    name varchar(10)
) engine = myisam;
```

##### 2.2 查看当前数据库支持的存储引擎

```sql
show engines;
```

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-03 162842.png)

#### 3. 存储引擎特点

##### 3.1 InnoDB

- InnoDB是一种兼顾高可靠性和高性能的通用存储引擎，在 MySQL 5.5 之后， InnoDB是默认的 MySQL 存储引擎

- 特点：

  - DML操作遵循ACID模型，支持**事务**；
  - **行级锁**，提高并发访问性能；
  - 支持**外键**FOREIGN KEY约束，保证数据的完整性和正确性；

- 文件：

  - xx.ibd：xxx代表的是表名，innoDB引擎的每张表都会对应这样一个**表空间文件**，存储该表的表结构（frm、sdi）、数据和索引。
  - 参数：innodb_file_per_table

  ![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-03 170235.png)

##### 3.2 MYISAM

- MySQL是MySQL早期的默认存储引擎
- 特点：
  - 不支持事务，不支持外键
  - 支持表锁，不支持行锁
  - 访问速度快
- 文件：
  - xxx.sdi：存储表结构信息
  - xxx.MYD：存储数据
  - xxx.MYI：存储索引

#####  3.3 Memoery

- Memory引擎的表数据时存储在内存中的，由于受到硬件问题、或断电问题的影响，只能将这些表作为临时表或缓存使用
- 特点：
  - 内存存放
  - hash索引（默认）
- 文件：xxx.sdi：存储表结构信息

##### 3.4 区别

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-03 171242.png)

##### 3.5 存储引擎的选择

- 在选择存储引擎时，应该根据应用系统的特点选择合适的存储引擎。对于复杂的应用系统，还可以根据实际情况选择多种存储引擎进行组合
- **InnoDB**：是Mysql的**默认存储引擎**，支持事务、外键。如果应用对事务的**完整性**有比较高的要求，在并发条件下要求**数据的一致性**，数据操作除了插入和查询之外，还包含很多的更新、删除操作，那么InnoDB存储引擎是比较合适的选择
- **MyISAM**：如果应用是以**读操作**和**插入操作**为主，只有很少的更新和删除操作，并且对事务的完整性、并发性要求不是很高，那么选择这个存储引擎是非常合适的，存储业务系统的非核心数据----**mongodb**
- **MEMORY**：将所有数据保存在内存中，**访问速度快**，通常用于临时表及缓存。MEMORY的缺陷就是**对表的大小有限制**，太大的表无法缓存在内存中，而且无法保障数据的安全性---**-redis**

### 二、索引

#### 1. 索引概述

- **索引(index)**是帮助MySQL**高效获取数据**的**数据结构(有序)**。

- 在数据之外，数据库系统还**维护着满足特定查找算法的数据结构**，这些数据结构以某种方式引用(指向)数据，这样就可以在这些数据结构上实现高级查找算法，这种数据结构就是索引。

  ![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-04 122317.png)

  注：上述二叉树索引结构的只是一个示意图，并不是真实的索引结构

  |                            优点                             |                             缺点                             |
  | :---------------------------------------------------------: | :----------------------------------------------------------: |
  |           提高数据检索的效率，降低数据库的IO成本            |                    索引列也是要占用空间的                    |
  | 通过索引列对数据进行排序，降低数据排序的成本，降低CPU的消耗 | 索引大大提高了查询效率，同时却也降低更新表的速度，如对表进行INSERT、UPDATE、DELETE时，效率降低 |

#### 2. 索引结构

##### 2.1 索引结构

MySQL的索引是在**存储引擎层**实现的，不同的存储引擎有不同的结构，主要包含以下几种：

|        索引结构         |                             描述                             |
| :---------------------: | :----------------------------------------------------------: |
|     *B+ Tree* 索引      |        **最常见的索引类型，大部分引擎都支持B+树索引**        |
|       *Hash* 索引       | 底层数据结构是用**哈希表**实现的，只有**精确匹配**索引列的查询才有效，不支持范围查询 |
|  *R-tree*（空间索引）   | 空间索引是MyISAM引擎的一个特殊索引类型，主要用于**地理空间数据类型**，通常使用较少 |
| *Full-text*（全文索引） | 是一种通过建立**倒排索引**，快速匹配文档的方式。类似于Lucene,Solr,ES |

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-04 122931.png)

##### 2.2 B+Tree 索引

- **二叉树**缺点：顺序插入时，会形成一个链表，查询性能大大降低。大数据情况下，层级较深，检索速度慢。

- **红黑树**：大数据量情况下，层级较深，检索速度慢。

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-04 123219.png)

- **B-Tree 多路平衡查找树**

  以一颗最大度数(max-degree)为5的b-tree为例(每个节点最多存储4个key，5个指针)：

  ![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-04 123526.png)

- **B+Tree**

	以一颗最大度数（max-degree）为4（4阶）的b+tree为例：

	![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-04 124218.png)

	相对于B-Tree区别：

	①所有的数据都会出现在叶子节点
	②叶子节点形成一个单向链表

- MySQL索引数据结构对经典的B+Tree进行了优化。在原B+Tree的基础上，增加一个指向相邻叶子节点的链表指针，就形成了带有顺序指针的B+Tree，提高区间访问的性能

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-04 124442.png)



##### 2.3 Hash索引

- 哈希索引就是采用一定的**hash算法**，将键值换算成新的hash值，映射到对应的槽位上，然后存储在hash表中。如果两个(或多个)键值，映射到一个相同的槽位上，他们就产生了hash冲突(也称为hash碰撞)，可以通过链表来解决

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-04 125413.png)

- hash索引特点：
  - Hash索引只能用于对等比较(=，in)，不支持范围查询(between，>，<，...
  - 无法利用索引完成排序操作
  - 查询效率高，通常只需要一次检索就可以了，效率通常要高于B+tree索引
- 存储引擎支持：在MySQL中，支持hash索引的是Memory引擎，而InnoDB中具有自适应hash功能，hash索引是存储引擎根据B+Tree索引在指定条件下自动构建的。



##### 2.4 为什么InnoDB存储引擎选择使用B+tree索引结构?

- 相对于二叉树，层级更少，搜索效率高；
- 对于B-tree，无论是叶子节点还是非叶子节点，都会保存数据，这样导致一页中存储的键值减少，指针跟着减少，要同样保存大量数据，只能增加树的高度，导致性能降低；
- 相对Hash索引， B+tree支持范围匹配及排序操作；

#### 3. 索引分类

|   分类   |                     含义                     |           特点           | 关键字  |
| :------: | :------------------------------------------: | :----------------------: | :-----: |
| 主键索引 |           针对于表中主键创建的索引           | 默认自动创建，只能有一个 | peimary |
| 唯一索引 |       避免同一个表中某数据列中的值重复       |        可以有多个        | unique  |
| 常规索引 |               快速定位特定数据               |        可以有多个        |    ✕    |
| 全文索引 | 查找的是文本中的关键字，而不是比较索引中的值 |        可以有多个        | fulltxt |

##### 3.1 InnoDB索引分类

- 在InnoDB存储引擎中，根据索引的存储形式，又可以分为以下两种：

|           分类            |                            含义                            |         特点         |
| :-----------------------: | :--------------------------------------------------------: | :------------------: |
| 聚集索引(Clustered Index) | 将数据存储与索引放到了一块，索引结构的叶子节点保存了行数据 | 必须有，而且只有一个 |
| 二级索引(Secondary Index) | 将数据与索引分开存储，索引结构的叶子节点关联的是对应的主键 |     可以存在多个     |

- 聚集索引选取规则:
  - 如果存在主键，主键索引就是聚集索引。
  - 如果不存在主键，将使用第一个唯一(UNIQUE)索引作为聚集索引。
  - 如果表没有主键，或没有合适的唯一索引，则InnoDB会自动生成一个rowid作为隐藏的聚集索引。

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-04 131147.png)

##### 3.2 回表查询

**回表查询**,指当执行查询时，先通过非主键索引（也叫二级索引）找到匹配记录的**主键 ID**，然后再通过主键索引（聚簇索引）查询到完整的行数据的过程。简单来说，就是查询需要 “**两次**” 访问索引：第一次查二级索引拿到主键，第二次用主键查聚簇索引拿到全部数据。

```sql
-- 1.以下SQL语句，那个执行效率高？为什么？
-- 备注：id为主键，name字段创建的有索引；

select* from user where id=10;
select* from user where name='Arm';

-- 2.InnoDB主键索引的B+tree高度为多高呢？
/*假设：
一行数据大小为1k，一页中可以存储16行这样的数据。InnoDB的指针占用6个字节的空间，主键即使为bigint，占用字节数为8。
高度为2：	n*8+(n+1)*6=16*1024，算出n约为1170 1171*16=18736
高度为3：	1171*1171*16=21939856

可以存储大量的数据，检索效率是很高的*/
```

#### 4. 索引语法

##### 4.1 创建索引

```sql
create (unique|fulltext) index index_name on table_name (index_col_name,···) ;

-- 未指定unique或fulltext则说明创建的是一个常规索引
-- ···表示一个索引可以关联多个字段（联合索引）/只关联一个（单列索引）
```

##### 4.2 查看索引

```sql
show index from table_name;
```

##### 4.3 删除索引

```sql
drop index index_name on table_name;
```

```sql
-- 1. name字段为姓名字段，该字段的值可能会重复，为该字段创建索引。
create index idx_user_name on tb_user(name);
 
-- 2. phone手机号字段的值，是非空，且唯一的，为该字段创建唯一索引。
 create unique index idx_user_phone on tb_user;
-- 3. 为profession、age、status创建联合索引。
-- 联合索引的顺序是有要求的
 create index idx_user_pro_age_sta on tb_user(profession,age,status);
 
-- 4. 为email建立合适的索引来提升查询效率。
 create index idx_user_email on tb_user(email);
 
 drop index idx_user_email on tb_user;
```



#### 5. SQL性能分析

##### 5.1 SQL执行效率

- MySQL客户端连接成功后，通过show[session|global]status命令可以提供服务器状态信息。通过如下指令，可以查看当前数据库的INSERT、UPDATE、DELETE、SELECT的访问频次：

  ```sql
  show (global|session) status like 'com_______'; -- 7个下划线
  
  -- global 查看全局
  -- session 查看当前会话
  ```

<img src="C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-04 144716.png" style="zoom:67%;" />

##### 5.2 慢查询日志

- 慢查询日志记录了所有执行时间超过指定参数（long query time，单位：秒，默认10秒）的所有SQL语句的日志。可以通过查询慢查询日志来确认哪些SQL语句需要优化

- MySQL的慢查询日志默认没有开启，需要在MySQL的配置文件（/etc/my.cnf）中配置如下信息：

  ```sql
  show variables like 'slow_query_log';
  
  #开启MySQL慢日志查询开关
  slow_query_log=1
  #设置慢日志的时间为2秒，SQL语句执行时间超过2秒，就会视为慢查询，记录慢查询日志
  long_query_time=2
  ```

  配置完毕之后，通过以下指令  ``systemctl restart mysqld``  重新启动MySQL服务器进行测试，查看慢日志文件中记录的信息/var/lib/mysql/localhost-slow.log

##### 5.3 profile详情

- 能够在做SQL优化时帮助我们了解时间都耗费到哪里去了,通过have profiling参数，能够看到当前MySQL是否支持profile操作

  ```sql
  select @@have_profiling;
  -- 默认profiling是关闭的，可以通过set语句在session/ global级别开启profiling:
  
  set profiling = 1;
  -- 同样可以用session|global来指定全局或局部
  
  -- @是用户变量，@@是系统变量
  ```

- 执行一系列的业务SQL的操作，然后通过如下指令查看指令的执行耗时：

  ```sql
  #查看每一条SQL的耗时基本情况
  
  show profiles;
  
  #查看指定query_id的SQL语句各个阶段的耗时情况
  
  show profile for query query_id;
  
  #查看指定query_id的SQL语句CPU的使用情况
  
  show profile cpu for query query_id;
  ```

  <img src="C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-04 152500.png" style="zoom: 67%;" />

##### 5.4 explain执行计划

- explain或者desc命令获取MySQL如何执行SELECT语句的信息，包括在SELECT语句执行过程中表如何连接和连接的顺序。是评判SQL语句性能的重要标志

- 语法:

  ```sql
  #直接在select语句之前加上关键字explain / desc
  explain select 字段列表 from 表名 where 条件;
  ```

  <img src="C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-04 153008.png" style="zoom:150%;" />

- explain执行计划各字段含义

  |     字段     |                             含义                             |
  | :----------: | :----------------------------------------------------------: |
  |      id      | select查询的序列号，表示查询中执行select子句或者是操作表的顺序(id相同，执行顺序从上到下；id不同，值越大，越先执行) |
  | select_type  | 表示SELECT的类型，常见的取值有SIMPLE（简单表，即不使用表连接或者子查询）、PRIMARY（主查询，即外层的查询）、UNION（UNION中的第二个或者后面的查询语句）、SUB查询（SELECT/WHERE之后包含了子查询）等 |
  |     type     | 表示连接类型，性能由好到差的连接类型为NULL、system、const、eq_ ref、ref、range、index、all |
  | possible_key |           显示可能应用在这张表上的索引，一个或多个           |
  |     key      |          实际使用的索引，如果为NULL，则没有使用索引          |
  |   key_len    | 表示索引中使用的字节数，该值为索引字段最大可能长度，并非实际使用长度，在不损失精确性的前提下，长度越短越好 |
  |     rows     | MySQL认为必须要执行查询的行数，在nnodb引擎的表中，是一个估计值，可能并不总是准确的 |
  |   filtered   | 表示返回结果的行数占需读取行数的百分比，filtered的值越大越好 |

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-04 154049.png)

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-04 154603.png)

- *type* 字段有多种类型，从性能最差到最好依次为：

1. **NULL**：MySQL 不用访问表或索引就能直接得到结果。
2. **CONST/SYSTEM**：单表中最多只有一条匹配行，查询非常迅速。
3. **EQ_REF**：使用唯一索引，对于每个索引键值，只有唯一的一条匹配记录。
4. **REF**：使用非唯一性索引或唯一索引的前缀扫描，返回匹配某个单独值的记录行。
5. **RANGE**：索引范围扫描，常见于 *<*、*<=*、*>*、*>=*、*BETWEEN* 等操作符。
6. **INDEX**：索引全扫描，MySQL 遍历整个索引来查找匹配的行。
7. **ALL**：全表扫描，MySQL 扫描整个表来找到匹配的行。



#### 6. 索引的使用

##### 6.1 最左前缀法则

- 如果索引了多列(**联合索引**),要遵守最左前缀法则,最左前缀法则指的是查询从索引的最左列开始,并且不跳过索引中的列

- 如果跳跃某一列，索引将部分失效(**后面的字段索引失效**),跳过最左侧列则会全部失效

  ![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-04 163633.png)

	当只查询age与status而跳过profession时,type从ref类型变为all类型,因为没有出现最左侧列导致后面索引失效

	如果跳过中间字段age,profession索引正常,而status索引失效

- 最左前缀法则**与字段顺序无关**,只要字段存在即可

##### 6.2 范围查询

- 联合索引中,出现范围查询(>,<),范围查询右侧的列索引失效

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-04 164531.png)

索引长度key_len为49,说明status索引失效了

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-04 164711.png)

但改为使用>=或<=时,索引正常

##### 6.3 索引列运算

- 不要再索引列上进行运算操作,否则索引将失效

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-04 165139.png)

对索引列进行了函数运算,索引失效,type变为all

##### 6.4 字符串类型

- 字符串类型字段使用时,不加引号,索引将失效
- 可以查询到数据,但不是通过索引方式

##### 6.5 模糊查询

- 如果是尾部模糊匹配,索引不会失效,但如果是头部模糊匹配会失效

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-04 170023.png)

##### 6.6 or连接

- 用or分隔开的条件,如果or前的条件中的列有索引,而后面的列中没有索引,那么所涉及到的索引都不会被使用

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-04 170916.png)

##### 6.7 数据分布影响

- 如果MySQL评估使用索引比全表更慢,则不使用索引

##### 6.8 SQL提示

- 是优化数据库的一个重要手段,简单来说,就是在SQL语句中加入一些人为提示来达到优化操作的目的
- use index/ignore index/force index

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-05 113836.png)

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-05 113944.png)

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-05 113944.png)

##### 6.9 覆盖索引

- **覆盖索引**是数据库查询优化中的一个重要概念。它指的是当一个索引包含（或覆盖）所有需要查询的字段的情况。在这种情况下，数据库可以直接使用索引来完成查询，而无需回到数据表中去查找或者处理数据。这样做的好处是可以显著减少数据库的I/O操作，从而提高查询效率。

- 覆盖索引的核心在于，查询操作能够仅通过索引就能获取所需的全部数据。尽量使用覆盖索引(查询使用了索引,并且需要返回的列,在该索引中已经全部能够找到),减少select *,select * 很容易产生回表查询

- **using index condition**:查找使用了索引，但是需要**回表查询**数据

  **using where; using index**:查找使用了索引，但是需要的数据都在索引列中能找到，所以不需要回表查询数据

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-05 115236.png)

#####  6.10 前缀索引

- 当字段类型为字符串 (varchar, text等) 时，有时候需要索引很长的字符串，这会让索引变得很大，查询时，浪费大量的磁盘IO，影响查询效率。此时可以只将**字符串的一部分前缀**建立索引，这样可以大大节约索引空间，从而提高索引效率。

```sql
create index idx_name on table_name(column(n));
-- n表示要提取字符串的前n个字符来构建索引
```

- 前缀长度:可以根据索引的选择性来决定

  **选择性**是指不重复的索引值（基数）和数据表的记录总数的比值，**索引选择性越高则查询效率越高**,唯一索引的选择性是1，这是最好的索引选择性，性能也是最好的。

```sql
-- 如何求选择性
select count(distinct email) / count(*) from table_name;
select count(distinct substring(email,1,5)) / count(*) from table_name;


create index idx_email_5 on tb_user(email(5));
```

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-05 120542.png)

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-05 121041.png)

##### 6.11 单列索引与联合索引

- 在业务场景中，如果存在多个查询条件，考虑针对于查询字段建立索引时，建议建立联合索引，而非单列索引
- 多条件联合查询时，MySQL优化器会评估哪个字段的索引效率更高，会选择该索引完成本次查询

#### 7. 索引设计原则

1. 针对于数据量较大(超过几十万)，且查询比较频繁的表建立索引。

2. 针对于常作为查询条件(where)、排序(order by)、分组(group by)操作的字段建立索引。
3. 尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高。
4. 如果是字符串类型的字段，字段的长度较长，可以针对于字段的特点，建立前缀索引。
5. 尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率
6. 要控制索引的数量，索引并不是多多益善，索引越多，维护索引结构的代价也就越大，会影响增删改的效率。
7. 如果索引列不能存储NULL值，请在创建表时使用NOT NULL约束它。当优化器知道每列是否包含NULL值时，它可以更好地确定哪个索引最有效地用于查询。



### 三、SQL优化

#### 1. 插入数据

##### 1.1 insert优化

```sql
-- 1.批量插入
Insert into tb test values(1,' Tom'),(2,' Cat'),(3,' Jerry');
-- 2.手动提交事务
start transaction;
insert into tb test values(1,' Tom'),(2,' Cat'),(3,' Jerry');
insert into tb test values(4,' Tom'),(5,' Cat'),(6,' Jerry');
insert into tb test values(7,' Tom'),(8,' Cat'),(9,' Jerry');
commit;
-- 3.主键顺序插入
主键乱序插入 : 8 1 9 21 88 2 4 15 89 5 7 3
主键顺序插入 : 1 2 3 4 5 7 8 9 15 21 88 89
```

##### 1.2 大批量插入数据

- 如果一次性需要插入大批量数据，使用insert语句插入性能较低，此时可以使用MySQL数据库提供的load指令进行插入

```sql
#客户端连接服务端时，加上参数--local-infile
mysql--local-infile -u root -p

#设置全局参数local _ file为1,开启从本地加载文件导入数据的开关
set global local _ file=1;

#执行load指令将准备好的数据，加载到表结构中
load data local infile '/root/sql1.log' into table 'tb_user' fields terminated by ',' lines terminated by '\n';
#每一个字段间使用逗号分割，每一行通过换行符\n分割
```

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-05 131707.png)

主键顺序插入性能高于乱序插入

#### 2. 主键优化

##### 2.1 InnoDB数据组织方式

- InnoDB存储引擎中，表数据都是**根据主键顺序组织存放**的，这种存储方式的表称为**索引组织表**(index organized table,**IOT**)
- **页分裂** : 页可以为空，也可以填充一半，也可以填充100%。每个页包含了2-N行数据(至少两行数据),   如果一行数据太大，会产生行溢出，行数据根据主键排列

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-05 132617.png)

​	一个页默认大小16K,每页之间维护一个**双向链表**,如果前一页满则申请下一页



![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-05 132855.png)

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-05 132941.png)

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-05 132834.png)

​	页分裂发生在向一个已满的页中插入新数据时，当前页无法容纳新记录。其过程包括：

1. **查找分裂点**：确定新记录插入的位置。
2. **创建新页**：将部分数据从原页移动到新页。
3. **更新父页**：如果是索引页，父页的索引需更新以指向新页。
4. **插入新记录**：将新数据插入到正确位置。

​	页分裂会导致数据在物理存储上的错位，增加查询复杂性，同时可能引发碎片化，降低性能。

- **页合并**:当删除一行记录时，实际上记录并没有被物理删除，只是记录被标记（flaged）为删除并且它的空间变得允许被其他记录声明使用。当页中删除的记录达到**MERGE THRESHOLD**（默认为页的50%），InnoDB会开始寻找最靠近的页（前或后）看看是否可以将两个页合并以优化空间使用。

  - **选择合并页**：查找相邻页以进行合并。
  - **移动数据**：将一个页的数据移动到另一个页。
  - **更新父页**：如果是索引页，父页的索引需更新。
  - **释放空间**：释放被合并页的存储空间。

  MERGE THRESHOLD:含并页的阈值，可以自己设置，在创建表或者创建索引时指定

  页合并可以优化空间利用率，但频繁的合并操作会增加 I/O 和 CPU 的负担。

##### 2.3 主键设计原则

- 满足业务需求的情况下，尽量降低主键的长度
- 插入数据时，尽量选择顺序插入，选择使用AUTO_INCREMENT自增主键,而不选用UUID（UUID，通用唯一标识符，是一种128位的数字，通常表示为五个十六进制数字的字符串格式，如*aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee*。在MySQL中，UUID可以用作表的主键，并且可以通过UUID()函数生成。）
- 尽量不要使用UID做主键或者是其他自然主键，如身份证号
- 业务操作时，避免对主键的修改

#### 3. order by优化

##### 3.1 using filesort

- 通过表的索引或全表扫描，读取满足条件的数据行，然后在排序缓冲区sort buffer中完成排序操作，所有**不是通过索引直接返回排序结果的排序**都叫FileSort排序,效率低于using index

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-05 134308.png)

##### 3.2 using index

- **通过有序索引顺序扫描直接返回有序数据**，这种情况即为using index，不需要额外排序，操作效率高

<img src="C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-05 134504.png" style="zoom:150%;" />

##### 3.3 说明

- 根据排序字段建立合适的索引，多字段排序时，也遵循最左前缀法则
- 尽量使用**覆盖索引**
- 多字段排序，一个升序一个降序，此时需要注意联合索引在创建时的规则(ASC/DESC)
- 如果不可避免的出现filesort，大数据量排序时，可以**适当增大排序缓冲区大小**sort _ buffer _ size(默认256k)

#### 4. group by优化

- 在分组操作时，可以通过索引来提高效率
- 分组操作时，索引的使用也是满足最左前缀法则的

#### 5. limit优化－－分页操作

- 一个常见又非常头疼的问题就是limit 2000000,10，此时需要MySQL排序前2000000记录，仅仅返回2000000 的记录，其他记录丢弃，查询排序的代价非常大

- 优化思路：一般分页查询时，通过创建覆盖索引能够比较好地提高性能，可以通过覆盖索引加子查询形式进行优化

  ```sql
  explain select * from tb_user t,(select id from tb_sku order by id limit 2000000,10) a where t.id =a.id;
  ```

  

#### 6. count优化

- MyISAM引擎把一个表的总行数存在了磁盘上，因此执行count(*)的时候会直接返回这个数，效率很高(没有where条件查询时)；InnoDB引擎就麻烦了，它执行count()的时候，需要把数据一行一行地从引擎里面读出来，然后累积计数。

- 优化思路：自己计数

- count的用法：count()是一个聚合函数，对于返回的结果集，一行行地判断，如果count函数的参数不是NULL，累计值就加1，否则不加，最后返回累计值。

  |     用法      |                             说明                             |
  | :-----------: | :----------------------------------------------------------: |
  |   count (*)   | InoDB引擎并不会把全部字段取出来，而是专门做了优化，不取值，服务层直接按行进行累加 |
  | count (主键)  | InnoDB引擎会遍历整张表，把每一行的主键id值都取出来，返回给服务层。服务层拿到主键后，直接按按进行累加(主键不可能为null) |
  | count（字段） | 没有null约束：innoDB引擎会遍历整张表把每一行的字段值都取出来，返回给服务层，服务层判断是否为null，不为null，计数累加<br/>    有null约束：innoDB引擎会遍历整张表把每一行的字段值都取出来，返回给服务层，直接接进行进行累加 |
  |  count（1）   | `count(1)` 与 `count(*)` 在功能上基本相同，都是统计记录总数。`1` 作为常量，在统计过程中，MySQL 会为每行记录生成一个 `1` 然后计数。<br/>InnDB引擎遍历整张表，但不取值。服务层对于返回的每一行，放一个数字“1”进去，直接按行进行累加 |

  按照效率排序的话，count(字段) < count(主键id) < count(1)≈count()，所以尽量使用count()

#### 7.update优化

- InnoDB的行锁是针对索引加的锁，不是针对记录加的锁，所以更新时一定要有索引，并且该索引不能失效，否则会从行锁升级为表锁

### 四、视图/存储过程/触发器

#### 1.视图

##### 1.1 概念

​	视图（View）是一种虚拟存在的表。视图中的数据并不在数据库中实际存在，行和列数据来自定义视图的查询中使用的表(基表)，并且是在使用视图时动态生成的。

​	通俗的讲，视图**只保存了查询的SQL逻辑**，不保存查询结果。所以我们在创建视图的时候，主要的工作就落在创建这条SQL查询语句上。

##### 1.2 语法

```sql
-- 1.创建视图
create (or replace) view 视图名称(列名列表) as select语句 (with(cascaded|local) check option);  
#as是一个虚拟表，具体的数据来源于后面的select语句
#select语句需要指定从哪一张表中查询数据，也就是这个视图所关联的基表

-- 2.查询创建视图语句
show create view 视图名称;
-- 3.查看视图数据
select * from 视图名称;

-- 4.修改
create or replace view 视图名称(列名列表) as select语句 (with(cascaded|local) check option); 
alter view 视图名称(列名列表) as select语句 (with(cascaded|local) check option);

-- 5.删除
drop view (if exists) 视图名称 (,视图名称)···;
```



```sql
create or replace view stu_v_1 as select id,name from student;

create or replace view stu_v_1 as select id,name,no from student;

alter view stu_v_1 as select name,no from student;

drop view if exists stu_v_1;

select * from stu_v_1;
insert into stu_v_1;
#向视图中插入数据，实际上还是向基表中插入数据
#但有时插入的数据不在视图条件内，基表中插入但是视图不会显示
#可以使用检查选项with cascaded|local check option来避免这种情况
```

##### 1.3 视图的检查选项

- 当使用CHECK OPTIONS句创建视图时，MySQL会通过视图检查正在更改的每个行，例如插入，新建，删除，以使其符合视图创建的定义，如果不符合则不允许操作。MySQL允许基于另一个视图创建视图，它还会检查依赖视图中的规则以**保持一致性**。为了确定检查的范围，mysql提供了两个选项：CASCADED和LOCAL，默认值为CASCADED

- cascaded选项：CASCADED会递归检查所有依赖的视图

  ```sql
  create or replace view stu_v_1 as select id,name from student where id <=20;
  
  insert into stu_v_1 values(5,"Tom");
  insert into stu_v_1 values(25,"alicw");
  #都可以成功，不会报错
  
  create or replace view stu_v_2 as select id,name from stu_v_1 where id >= 20 with cascaded check option;
  #基于一个视图来创建新视图
  
  insert into stu_v_2 values(7,"jack");
  #会报错，cascaded选项不允许不符合定义的操作执行
  
  insert into stu_v_2 values(26,'cindy');
  #同样会报错，cascaded不仅检查stu_v_2,还检查它所依赖的视图stu_v_1是否符合条件
  
  create or replace view stu_v_3 as select id,name from stu_v_2 where id <=15;
  
  insert into stu _v_3 values(11,'Tom') #可以执行
  insert into stu _v_3 values(17,'Tom') #可以执行，满足v1，v2条件，而v3不用检查
  insert into stu _v_3 values(28,'Tom') #不可以执行，不满足底层选项
  ```

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-07 151654.png)

- local选项：LOCAL只检查当前视图的依赖项

  ```sql
  create or replace view stu_v4.  a select id, name from student where id <= 15
  insert into stu_v_4 values(5,'Tom');
  insert into stu_v_4 values(16,'Tom');
  #都执行成功
  
  create or replace view stu_v_5 as select id, name from stu_v_4 where id >=18 with local check option ;
  insert into stu_v_5 values(13,'Tom');
  #先检查是否满足V5条件，再递归检查v4，如果v4有条件则检查v4，没有则不检查
  insert into stu_v_5 values(17,'Tom');
  #都执行成功
  
  
  create or replace view stu_v_6 as select id, name from stu_v_5 where id <20;
  insert into stu_v_3 values(14,'Tom');
  ```

  local不会对上级条件再增加约束，而cascaded会增加

  ![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-07 151505.png)

##### 1.5 视图的更新

- 使视图可更新，视图中的行与基础表中的行之间必须**存在一对一的关系**。如果视图包含以下任何一项，则该视图不可更新：

  1. 聚合函数或窗口函数(SUM()、MIN()、MAX()、COUNT()等)
  2. DISTRICT
  3. GROUP BY
  4. HAVING
  5. UNION或者UNION ALL

  ```sql
  create view stu_v_count as select count(*) from student;
  insert into stu_v_count values(10);
  #[HY000][1471] The target table stu_v_count of the INSERT is not insertable-into
  #视图中有聚合函数，不能进行更新和插入操作
  ```

##### 1.6 视图的作用

- 简单：视图不仅可以简化用户对数据的理解，也可以简化他们的操作。那些被经常使用的查询可以被定义为视图，从而使得用户不必为以后的操作每次指定全部的条件
- 安全：数据库可以授权，但不能授权到数据库特定行和特定的列上。通过视图用户只能查询和修改他们所能见到的数据
- 数据独立：视图可帮助用户屏蔽真实表结构变化带来的影响

##### 1.7 视图案例

```sql
-- 1. 为了保证数据库表的安全性，开发人员在操作tb_user表时，只能看到的用户的基本字段，屏蔽手机号和邮箱两个字段。
-- 2. 查询每个学生所选修的课程（三张表联查），这个功能在很多的业务中都有使用到，为了简化操作，定义一个视图。


create view tb_user_view as select id,name,profession,age,gender from tb_user;

select * from tb_user_view;


create view tb_sc_view as select s.name student_name,s.no student_no,c.name course_name from student s,student_course sc,course c where s.id=sc.studentid and sc.courseid=c.id;

select * from tb_sc_view;

#在多表联查是只需查询view视图表，而不需要再写复杂的SQL语句
```



#### 2.存储过程

##### 2.1 概念

- 存储过程是**事先经过编译并存在数据库中的一段 SQL 语句的集合**，调用存储过程可以简化应用开发人员的很多工作，减少数据在数据库和应用服务器之间的传输，对于提高数据处理的效率是有好处的
- 存储过程思想上很简单，就是数据库 SQL 语言层面的**代码封装与重用**

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-08 151624.png)

##### 2.2 特点

- 封装，复用
- 可以接收参数，也可以返回数据
- 减少网络交互，效率提升（作用）

##### 2.3 基本语法

```sql
-- 1.创建
create procedure 存储过程名称(参数列表)
begin
	--sql语句
end;

-- 2. 调用
call 名称(参数);

-- 3.查看
select * from information_schema.routines where routine_schema='xxx'; -- 查询指定数据库的存储过程和状态信息
show create procedure 存储过程名称; -- 查询存储过程的定义

-- 4.删除
drop procedure (if exists) 存储过程名称;
```

```sql
create procedure p1()
begin
    select count(*) from student;
end;
#注意：在命令行中，执行创建存储过程的SQL时，需要通过关键字 delimiter 指定SQL语句的结束符

call p1();

select * from information_schema.routines where routine_schema='itcast';

show create procedure p1;

drop procedure if exists p1;
```

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-08 152913.png)

##### 2.3 变量

1. **系统变量**是MySQL服务器提供，不是用户定义的，属于服务器层面。分为**全局变量(GLOBAL)、会话变量(SESSION)**,一般默认是session

```sql
-- 查看系统变量
show (session|global) variables; #查看所有系统变量
show (session|global) variables like '___';#使用模糊匹配查看变量
select @@(session|global) 系统变量名; #查看指定变量的值

-- 设置系统变量
set (session|global) 系统变量名=值;
set @@(session|global) 系统变量名=值;
```

```sql
show variables;
show session variables like 'auto%';
show global variables like 'auto%';
select @@autocommit;

select @@session.autocommit;
```



- **注意**：mysql服务重新启动之后，所设置的全局参数会失效，要想不失效，可以在/etc/my.cnf中配置

2. **用户定义变量**是用户根据需要自己定义的变量，用户变量不用提前声明，在用的时候直接用“@变量名”使用就可以，其作用域为当前会话

```sql
-- 赋值
set @var_name=expr (,@var_name=expr,···);
set @var_name:=expr (,@var_name=expr,···);

select @var_name=expr (,@var_name=expr,···);
select 字段名 into @var_name from 表名; #将表中某一个字段给变量赋值

-- 使用
select @var_name;
```

- 注意：用户定义的变量无需对其进行声明或初始化，只不过获取到的值为NULL

3. **局部变量**是根据需要定义的在局部生效的变量，访问之前，**需要DECLARE声明**。可用作存储过程内的局部变量和输入参数，局部变量的范围是在其内声明的BEGIN……END块

```sql
-- 声明
declare 变量名 变量类型(defalut···);
#变量类型就是数据库字段类型：int,bigint,varchar,date,time等

-- 赋值
set 变量名=值；
set 变量名：=值；
select 字段名 into 变量名 from 表名 ···；
```

```sql
create procedure p2()
begin
    declare stu_count int default 0;
    set stu_count := 100;
    #select count(*) into stu_count from student;
    select stu_count;
end;

call p2();
```



##### 2.5 if条件判断

```sql
if 条件1 then
	···
elseif 条件2 then
	···
else 
	···
end if;
```

```sql
/*
根据定义的分数score变量，判定当前分数对应的分数等级。
1. score>=85分，等级为优秀。
2. score>=60分且score<85分，等级为及格。
3. score<60分，等级为不及格。*/

create procedure p3()
begin
    declare score int default 58;
    declare result varchar(10);

    if score >= 85 then
        set result :='A';
    elseif score >= 60 then
        set result := 'B';
    else
        set result := 'C';
    end if;
    select result;
end;

call p3();
```



##### 2.6 参数

| 类型  |                     含义                     | 备注 |
| :---: | :------------------------------------------: | :--: |
|  in   |   该类参数作为输入，也就是需要调用时传入值   | 默认 |
|  out  | 该类参数作为输出，也就是该参数可以作为返回值 |      |
| inout |    既可以作为输入参数，也可以作为输出参数    |      |

- 语法：

```sql
create procedure 存储过程名称(in|out|inout 参数名 参数类型)
begin
	--SQL语句
end;


-- 分数等级
create procedure p4(in score int,out result varchar(10))
begin

    if score >= 85 then
        set result :='A';
    elseif score >= 60 then
        set result := 'B';
    else
        set result := 'C';
    end if;
end;

call p4(80,@result);
select @result;

-- 将传入的200分制的分数，进行换算，换算成百分制，然后返回分数---> inout create procedure p5(inout score double)
begin
	set score := score * 0.5;
	end;
call p5(score: Qscore);
select Qscore;
```



##### 2.7 case分支判断

- 语法：

```sql
case case_value
	when when_value1 then statement1
	when when_value2 then statement2
	···
	else statement
end case;

case 
	when condition1 then statement1
	when condition2 then statement2
	···
	else statement
end case;
```

```sql
-- 根据传入的月份，判定月份所属的季节（要求采用case结构）
create procedure p6(in month int)
begin
    declare result varchar(10);

    case
        when month>=1 and month <= 3 then
            set result :='yi';
        when month>=4 and month <= 6 then
            set result :='er';
        when month>=7 and month <= 9 then
            set result :='san';
        when month>=10 and month <= 12 then
            set result :='si';
        else
            set result := 'error';
    end case;

    select concat(month,'is',result);
end;

call p6(4);
```



##### 2.8 while 循环

- while循环是有条件的循环控制语句。满足条件后，再执行循环体中的SQL语句。具体语法为：

```sql
while 条件 do
	SQL逻辑···
end while;

#先判定条件，true则执行，否则跳过
```

```sql
-- A.定义局部变量，记录累加之后的值；
-- B.每循环一次，就会对n进行减1，如果n减到0，则退出循环
create procedure p7(in n int)
begin
    declare total int default 0;
    while n>0 do
        set total:= total +n;
        set n:=n-1;
        end while;
    select total;
end;

call p7(4);
```



##### 2.9 repeat 循环

- repeat是有条件的循环控制语句，当满足条件的时候**退出循环**。具体语法为：

```sql
#先执行一次逻辑，然后判定逻辑是否满足，如果满足，则退出。如果不满足，则继续下一次循环
repeat 
	SQL逻辑
	until 条件
end repeat;
```

```sql
create procedure p8(in n int)
begin
    declare total int default 0;
    repeat
        set total:= total +n;
        set n:=n-1;
        until n=0  #这里没有分号
        end repeat;
    select total;
end;

call p8(20);
```



##### 2.10 loop循环

- LOOP 实现简单的循环，如果不在SQL逻辑中增加退出循环的条件，可以用其来实现简单的**死循环**。LOOP可以配合以下语句使用
  - leave：配合循环使用，退出循环
  - iterate：必须用在循环中，作用是跳过当前循环剩下的语句，直接进入下一次循环

```sql
(begin_label:)loop
	SQL逻辑
end loop(end_label);

leave label; -- 退出循环
iterate label; -- 直接进入下一次循环
```

```sql
-- loop 计算从1到n之间的偶数累加的值，n为传入的参数值。
-- A. 定义局部变量，记录累加之后的值；
-- B. 每循环一次，就会对n进行-1，如果n减到0，则退出循环----> leave
-- C. 如果当前累加的数据是奇数，则直接进入下一次循环

create procedure p10(in n int)
begin
    declare total int default 0;

    sum:loop
        if n<=0 then
            leave sum;
        end if;

        if n%2=1 then
            set n:=n-1;
            iterate sum;
        end if;
        set total:=total +n;
        set n:=n-1;
    end loop sum;
    select total;
end;

call p10(9);
```

##### 2.11 游标cursor

- 游标是用来存储查询结果集的数据类型，在存储过程和函数中**可以使用游标对结果集进行循环的处理**。游标的使用包括游标的声明、OPEN、FETCH和CLOSE，其语法分别如下

```sql
-- 声明
declare 游标名称 cursor for 查询语句;

-- 打开
open 游标名称;

-- 获取游标记录
fetch 游标名称 into 变量(,变量···);

-- 关闭游标
close 游标名称;
```

```sql
#根据传入的参数uage，来查询用户表th_user中，所有的用户年龄小于等于uage的用户姓名(name)和专业(profession)
#并将用户的姓名和专业插入到所创建的一张新表(id,name,profession)中。

-- 逻辑：
-- A.声明游标，存储查询结果集
-- B.准备：创建表结构
-- C.开启游标
-- D.获取游标中的记录
-- E.插入数据到新表中
-- F.关闭游标


```

- **注意：**游标和普通变量的声明是有顺序的，要先声明普通变量再声明游标

##### 2.12 条件处理程序

- 条件处理程序(Handler)可以用来定义在流程控制结构执行过程中**遇到问题时相应的处理步骤**。具体语法为：

```sql
declare handler_action handler for condition_value (,condition_value)··· statement;

handler_action
	continue:继续执行当前程序
	exit:终止执行当前程序
condition_value
	SQLstate sqlstate_value:状态码，如02000
	SQLwarning:所有以01开头的SQLstate代码的简写
	NOT FOUND:所有以02开头的SQLstate代码的简写
	SQLexception:所有没有被SQLwarning或notfound捕获的SQLstate代码的简写
```

```sql
create procedure p11(in uage int)
begin
    declare uname varchar(50);
    declare upro varchar(100);
    declare u_cursor cursor for select name,profession from tb_user where age <= uage;
    declare exit handler for sqlstate '02000' close u_cursor;

    create table if not exists tb_user_pro(
        id int primary key auto_increment,
        name varchar(50),
        profession varchar(100)
    )ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

    open u_cursor;-- 游标是一个集合，需要通过循环遍历
    while true do
        fetch u_cursor into uname,upro;
        insert into tb_user_pro values(null,uname,upro);
        end while;
    close u_cursor;

end;

call p11(30);
```

#### 3.存储函数

- 存储函数是有返回值的存储过程，存储函数的参数**只能是IN类型**的。具体语法如下：

```sql
create function 存储函数名称(参数列表)
returns type (characteristic)--当前存储参数特性
begin
	--SQL语句
	return···;
end;

characteristic说明：
deterministic：相同的输入参数总是产生相同的结果
NO SQL：不包含SQL语句。
reads SQL data：包含读取数据的语句，但不包含写入数据的语句。
```

```sql
create function fun1(n int) -- 默认in类型
returns int deterministic
begin
    declare total int default 0;

    while n>0 do
        set total:= total+n;
        set n:= n-1;

        end while;

    return total;
end;

select fun1(100);

```

- 存储函数使用较少，能用存储函数实现的都能用存储过程实现

#### 4. 触发器

##### 4.1 介绍

- 触发器是与表有关的数据库对象，指**在insert/update/delete之前或之后**，触发并执行触发器中定义的SQL语句集合。触发器的这种特性可以协助应用在数据库端确保数据的完整性，日志记录，数据校验等操作。
- 使用别名old和new来引用触发器中发生变化的记录内容，这与其他的数据库是相似的。现在触发器还只支持行级触发，不支持语句级触发。

|   触发器类型   |                       new和old                       |
| :------------: | :--------------------------------------------------: |
| insert型触发器 |            new表示将要或者已经新增的数据             |
| update型触发器 | old表示修改之前的数据，new表示将要或已经修改后的数据 |
| delete型触发器 |            old表示将要或者已经删除的数据             |

- **语句级触发器**是在执行整个SQL语句后触发的触发器。也就是说，当对表执行操作（插入、更新或删除）时，触发器只会在语句执行完毕后触发一次。
- **行级触发器**是指当每行受影响时触发的触发器。即当对表执行操作（插入、更新或删除）时，触发器会对每一行进行操作

##### 4.2 语法

```sql
-- 创建
create trigger trigger_name
before/after insert/update/delete
on table_name for each row -- 行级触发器
begin
	trigger_statement;
end;

-- 查看
show triggers;

-- 删除
drop trigger(schema_name.)trigger_name;
#如果没有指定schema_name，默认为当前数据库
```



```sql
-- 插入数据
create trigger tb_insert_trigger
    after insert on tb_user for each row
begin
    insert into user_logs(id, operation, operate_time, operate_id, operate_params)
        values(null,'insert',now(),new.id,concat('输入的内容为：id=',new.id,'name=',new.name));
end;

show triggers;

drop trigger tb_insert_trigger;

-- 插入数据到tb_user表中
insert into tb_user(id, name, phone, email, profession, age, gender, status, createtime) VALUES (26,'三皇子','18809091212','erhuangzi@163.com','软件工程',23,'1','1',now());

-- 修改触发器
create trigger tb_update_trigger
    after update on tb_user for each row
begin
    insert into user_logs(id, operation, operate_time, operate_id, operate_params)
        values (null, 'update', now(), new.id,
        concat('更新之前的数据: id=',old.id,',name=',old.name, ', phone=', old.phone, ', email=', old.email, ', profession=', old.profession,
            ' | 更新之后的数据: id=',new.id,',name=',new.name, ', phone=', NEW.phone, ', email=', NEW.email, ', profession=', NEW.profession));

end;

-- 删除触发器
update tb_user set profession = '会计' where id = 23;

create trigger tb_user_delete_trigger
    after delete on tb_user for each row
begin
    insert into user_logs(id, operation, operate_time, operate_id, operate_params) VALUES
    (null, 'delete', now(), old.id,
        concat('删除之前的数据: id=',old.id,',name=',old.name, ', phone=', old.phone, ', email=', old.email, ', profession=', old.profession));
end;

delete from tb_user where id = 26;
```



![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-10 173056.png)

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-10 173135.png)

### 五、锁

#### 1. 概述

##### 1.1 介绍

- 锁是计算机协调多个进程或线程**并发访问某一资源**的机制。在数据库中，除传统的计算资源（CPU、RAM、I/O）的争用以外，数据也是一种供许多用户共享的资源。如何保证数据并发访问的一致性、有效性是所有数据库必须解决的一个问题
- 锁冲突也是影响数据库并发访问性能的一个重要因素。从这个角度来说，锁对数据库而言显得尤其重要，也更加复杂

##### 1.2 分类

- MySQL中的锁，按照锁的粒度分，分为以下三类：
  1. 全局锁：锁定数据库中的所有表
  2. 表级锁：每次操作锁住整张表
  3. 行级锁：每次操作锁住对应的行数据





#### 2. 全局锁

##### 2.1 介绍    

- 全局锁就是对整个数据库实例加锁，加锁后整个实例就处于只读状态，后续的DML的写语句，DDL语句，已经更新操作的事务提交语句都将被**阻塞**
- 其典型的使用场景是做全库的逻辑备份，对所有的表进行锁定，从而获取一致性视图，保证数据的完整性。

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-10 174100.png)

##### 2.2 语法

```sql
flush tables with read lock;
#给所有表加上全局锁

mysqldump -uroot -ppassword itcast>itcast.sql
#-u 指定备份时连接数据库的用户名
#要将itcast数据库存到itcast.sql文件中

unlock tables;
#释放锁
```

- 备份过程中数据库只能读不能写

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-10 175012.png)

##### 2.3 不足

- 数据库中加全局锁，是一个比较重的操作，存在以下问题：
  1. 如果在主库上备份，那么在备份期间都不能执行更新，业务基本上就得停摆。
  2. 如果在从库上备份，那么在备份期间从库不能执行主库同步过来的二进制日志(binlog)，会导致主从延迟。
- 在InnoDB引擎中，我们可以在备份时加上参数--single-transaction参数来完成不加锁的一致性数据备份 `mysqldump --single-transaction -uroot -p123456 itcast>itcast.sql`

#### 3. 表级锁

- 表级锁，每次操作锁住整张表。锁定粒度大，发生锁冲突的概率最高，并发度最低。应用在MyISAM、InnoDB、BDB等存储引擎中

- 对于表级锁，主要分为以下三类：
  1. 表锁
  2. 元数据锁(meta data lock, MDL)
  3. 意向锁

##### 3.1 表锁

- 对于表锁，分为两类：表共享读锁（read lock），表独占写锁（write lock）

```sql
-- 加锁
lock tables 表名(,表名···) read/write;
#可以同时对多个表加表锁

-- 释放锁
unlock tables;
#或者客户端断开连接
```

- read lock:
  - 对于当前会话，只能读，不能写，会话会报错，其他会话update等操作时阻塞，直到释放锁后会执行

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-10 180255.png)

- write lock:
  - 对于当前会话，既能读，又能写，其他会话读写均不可

##### 3.2 元数据锁（MDL）

- MDL锁加过程是系统自动控制，无需显式使用，在访问一张表的时候会自动加上。MDL锁主要作用是维护表元数据的数据一致性，在表上有**活动事务**的时候，不可以对元数据进行写入操作。**主要时为了避免DML与DDL冲突，保证读写的正确性**
-  在MySQL5.5中引入了MDL，当对一张表进行增删改查的时候，加MDL读锁(共享)；当对表结构进行变更时操作的时候，加MDL写锁(排他)。

|                   SQL语句                   |                锁类型                 |                       说明                       |
| :-----------------------------------------: | :-----------------------------------: | :----------------------------------------------: |
|         lock tables xxx read/ write         | shared_read_only/shared_no_read_write |                                                  |
| select、select…lock in share mode（共享锁） |              shared_read              | 与SHARED READ、SHARED WRITE兼容，与EXCLUSIVE互斥 |
|  insert、update、delete、select…for update  |             shared_write              | 与SHARED READ、SHARED WRITE兼容，与EXCLUSIVE互斥 |
|               alter table···                |               exclusive               |                与其他的MDL都互斥                 |

- 查看元数据锁：`select object_type,object_schema,object_name,lock_type,lock_duration from performance_schema.metadata_locks;`



##### 3.3 意向锁

- 为了避免DML在执行时，加的行锁与表锁的冲突，在InnoDB中引入了意向锁，使得表锁不用检查每行数据是否加锁，使用意向锁来减少表锁的检查。提高了数据库并发操作的性能和效率。

- 意向锁分两种：

1. 意向共享锁(IS)：由语句select..lock in share mode添加。与表锁共享锁（read）兼容，与表锁排它锁（write）互斥
2. 意向排他锁(IX)：由insert、update、delete、select...for update添加。与表锁共享锁（read）及排它锁（write）都互斥。**意向锁之间不会互斥** 

- 查看意向锁及行锁加锁情况

  `select object_schema,object_name,index_name,lock_type,lock_mode,lock_date from performance_schema.data_locks;`



#### 4.行级锁

##### 4.1 介绍

- 行级锁，每次操作锁住对应的行数据。锁定粒度最小，发生锁冲突的概率最低，并发度最高。应用在imDB存储引擎中。

- InnoDB的数据是基于索引组织的，行锁是通过**对索引上的索引项加锁**来实现的，而不是对记录加的锁。

##### 4.2 分类

- 主要分三类：

1. 行锁（Record Lock）锁定单个行记录的锁，防止其他事务对此行进行update和delete。在RC、RR隔离级别下都支持。
2. 间隙锁（Gap Lock）：锁定索引记录间隙（不含该记录），确保索引记录间隙不变，防止其他事务在这个间隙进行insert，产生幻读。在RR隔离级别下都支持。
3. 临键锁（Next-Key Lock）：行锁和间隙锁组合，同时锁住数据，并锁住数据前面的间隔Gap。在RR隔离级别下支持。

##### 4.3 行锁

- InnoDB实现了以下两种类型的行锁：

1. 共享锁（S）：允许一个事务去读一行，阻止其他事务获得相同数据集的排它锁。S与S共享，但S与X互斥

2. 排他锁（X）：允许获取排他锁的事务更新数据，阻止其他事务获得相同数据集的共享锁和排他锁。

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-10 190024.png)

|            SQL语句            |  行锁类型  |                  说明                   |
| :---------------------------: | :--------: | :-------------------------------------: |
|           *insert*            |   排他锁   |                自动加锁                 |
|           *update*            |   排他锁   |                自动加锁                 |
|           *delete*            |   排他锁   |                自动加锁                 |
|           *select*            | 不加任何锁 |                                         |
| *select···lock in share mode* |   共享锁   | 需要手动在select后加 lock in share mode |
|     *select···for update*     |   排他锁   |     需要手动在select后加 for update     |

- 默认情况下，InnoDB在REPEATABLE READ事务隔离级别运行，InnoDB使用next-key锁进行搜索和索引扫描，以防止幻读。

1. 针对唯一索引进行检索时，对已存在的记录进行等值匹配时，将会自动优化为行锁。
2. InnoDB的行锁是针对于索引加的锁，**不通过索引条件检索数据，那么InnoDB将对表中的所有记录加锁，此时就会升级为表锁**。

##### 4.3 间隙锁&临键锁

- 默认情况下，InnoDB在REPEATABLE READ事务隔离级别运行，InnoDB使用next-key锁进行搜索和索引扫描，以防止幻读。

1. 索引上的等值查询(唯一索引)，给不存在的记录加锁时，优化为间隙锁。
2. 索引上的等值查询(普通索引)，向右遍历时最后一个值不满足查询需求时，next-key lock退化为间隙锁。
3. 索引上的范围查询(唯一索引)--会访问到不满足条件的第一个值为止。

- 注意：间隙锁唯一目的是防止其他事务插入间隙。间隙锁可以共存，一个事务采用的间隙锁不会阻止另一个事务在同一间隙上采用间隙锁。





### 六、InnoDB引擎

#### 1.逻辑存储结构

​	Linux系统下，数据文件存储在/var/lib/mysql目录下

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-10 192506.png)

表空间(ibd文件)：一个mysql实例可以对应多个表空间，用于存储记录、索引等数据。

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-10 193206.png)

- 段，分为数据段(Leaf node segment)、索引段(Non-leaf node segment)、回滚段(Rollback segment)、InnoDB是索引组织表，数据段就是B+树的叶子节点，索引段即为B+树的非叶子节点。段用来管理多个Extent(区)。
- 区，表空间的单元结构，每个区的大小为1M。默认情况下，InnoDB存储引擎页大小为16K，即一个区中一共有64个连续的页。
- 页，是InnoDB存储引擎磁盘管理的最小单元，每个页的大小默认为16KB。为了保证页的连续性，InnoDB存储引擎每次从磁盘申请4-5个区。表结构中所存储的记录、索引都是在页中存储的
- 行，InnoDB 存储引擎数据是按行进行存放的。
  - Trx_id：每次对某条记录进行改动时，都会把对应的事务id赋值给trx_id隐藏列。
  - Roll _ pointer：每次对某条记录进行改动时，都会把旧的版本写入undo日志中，然后这个隐藏列就相当于一个指针，可以通过它找到该记录修改前的信息。



#### 2.架构

- MySQL 5.5 版本开始，默认使用InnoDB存储引擎，它擅长事务处理，具有崩溃恢复特性，在日常开发中使用非常广泛。下面是InnoDB架构图，左侧为内存结构，右侧为磁盘结构。

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-10 194033.png)

##### 2.1 内存结构

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-10 194318.png)

- **Buffer Pool** 缓冲池：是主内存中的一个区域，里面可以缓存磁盘上经常操作的真实数据，在执行增删改查操作时，先操作缓冲池中的数据（若缓冲池没有数据，则从磁盘加载并缓存），然后再以一定频率刷新到磁盘，从而减少磁盘IO，加快处理速度。

​	缓冲池以Page页为单位，底层采用链表数据结构管理Page。根据状态，将Page分为三种类型：

1. free page:空闲page,未被使用。
2. clean page:被使用page,数据没有被修改过。
3. dirty page:脏页，被使用page,数据被修改过，也中数据与磁盘的数据产生了不一致。



- **Change Buffer** 更改缓冲区：针对于非唯一的二级索引页（除唯一索引或主键索引以外），在执行DML语句时，如果这些数据Page没有在Buffer Pool中，不会直接操作磁盘，而会将数据变更存在更改缓冲区Change Buffer中，在未来数据被读取时，再将数据合并恢复到Buffer Pool中，再将合并后的数据刷新到磁盘中。

​	**Change Buffer的意义是什么？**
​        与聚集索引不同，二级索引通常是非唯一的，并且以相对随机的顺序插入二级索引。同样，删除和更新可能会影响索引树中不相邻的二级索引页，如果每一次都操作磁盘，会造成大量的磁盘IO。有了ChangeBuffer之后，我们可以在缓冲池中进行合并处理，**减少磁盘IO。**



- **Adaptive Hash Index 自适应hash索引**：用于优化对Buffer Pool数据的查询。InnoDB存储引擎会监控对表上各索引页的查询，如果观察到hash索引可以提升速度，则自动建立hash索引，称之为自适应hash索引。

  自适应哈希索引，无需人工干预，是系统根据情况自动完成。
  参数: adaptive_hash_index，`show variables like '%hash_index%'`来查看是否开启

  ![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-10 195249.png)



- **Log Buffer 日志缓冲区**：用来保存要写入到磁盘中的log日志数据(redo log、undo log)，默认大小为16MB，日志缓冲区的日志会定期刷新到磁盘中。如果需要更新、插入或删除许多行的事务，增加日志缓冲区的大小可以节省磁盘I/O。

  参数：
  innodb_log_buffer_size：缓冲区大小
  innodb_flush_log_at_trx_commit：日志刷新到磁盘时机

     1：日志在每次事务提交时写入并刷新到磁盘
     0：每秒将日志写入并刷新到磁盘一次。
     2：日志在每次事务提交后写入，并每秒刷新到磁盘一次。



- 如果mysql有一台专门的服务器，那么通常来说这台服务器80%都会分配给缓冲区，以此来提高MySQL的执行效率



##### 2.2 磁盘结构

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-10 195756.png)

- **System Tablespace 系统表空间**：是更改缓冲区的存储区域。如果表是在系统表空间而不是每个表文件或通用表空间中创建的，它也可能包含表和索引数据。(在MySQL5.x版本中还包含InnoDB数据字典、undolog日志等)
  参数：inndb_data_file_path
- **File-Per-Table Tablespaces 每个表独立的文件表空间**：包含单个InnoDB表的数据和索引，并存储在文件系统上的单个数据文件中。
  参数: inndb_file_per_table

- **General Tablespaces 通用表空间**：需要通过 CREATE TABLESPACE 语法创建通用表空间，在创建表时，可以指定让某个表存储在表空间中。

  创建语法：`create tablespace xxx add datafile 'filename' engine=engine_name;`

​			  `create table xxx··· tablespace ts_name`

- **Undo Tablespace 撤销表空间**：MySQL实例在初始化时会自动创建两个默认的undo表空间(初始大小16M，undo_001和undo_002)，用于存储undo_log日志。
- **Temporary Tablespaces** 临时表空间：innoDB使用会话临时表空间和全局临时表空间。存储用户创建的临时表等数据。



- **Doublewrite Buffer Files 双写缓冲区**：innoDB引擎将数据页从Buffer Pool刷新到磁盘前，为了安全性，先将数据页写入双写缓冲区文件中，便于系统异常时恢复数据。(#ib_16384_0.dblwr,#ib_16384_1.dblwr)
- **Redo Log 重做日志**：是用来实现**事务的持久性**。该日志文件由两部分组成：重做日志缓冲（redo log buffer）以及重做日志文件（redo log），前者是在内存中，后者在磁盘中。当事务提交之后会把所有修改信息都会存到该日志中，用于在刷新脏页到磁盘时，发生错误时，进行数据恢复使用。redo log文件会按时清理。以循环方式写入重做日志文件（ib_logfile0,ib_logfile1）



##### 2.3 后台线程

- 将innoDB存储引擎中缓冲池的数据，在合适的时机刷新到磁盘文件中
- 主要分为四类：

1. Master Thread 核心后台线程
   负责调度其他线程，还负责将缓冲池中的数据异步刷新到磁盘中，保持数据的一致性，还包括脏页的刷新、合并插入缓存、undo页的回收。

2. IO Thread
   在InnoDB存储引擎中大量使用了AIO来处理IO请求，这样可以极大地提高数据库的性能，而IO Thread主要负责这些IO请求的**回调**。

   |       线程类型       | 默认个数 |             职责             |
   | :------------------: | :------: | :--------------------------: |
   |     Read thread      |    4     |          负责读操作          |
   |     Write thread     |    4     |          负责写操作          |
   |      Log thread      |    1     |  负责将日志缓冲区刷新到磁盘  |
   | Insert buffer thread |    1     | 负责将写缓冲区内容刷新到磁盘 |

3. Purge Thread
   主要用于回收事务已经提交了的undo log，在事务提交之后，undo log可能不用了，就用它来回收。

4. Page Cleaner Thread
   协助Master Thread刷新脏页到磁盘的线程，它可以减轻Master Thread的工作压力，减少阻塞。



#### 3.事务原理

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-10 203315.png)

##### 3.1 redo log

- 重做日志，记录的是事务提交时数据页的物理修改，是用来实现事务的持久性。
- 该日志文件由两部分组成：重做日志缓冲(redo log buffer)以及重做日志文件(redo log file)，前者是在内存中，后者在磁盘中。当事务提交之后会把所有修改信息都存到该日志文件中，用于在刷新脏页到磁盘，发生错误时，进行数据恢复使用。![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-10 204358.png)
- 当对缓冲区数据进行增删改后，会将增删改的数据记录在redolog buffer中，保存数据页的变化，当事务提交的时候，它会将redolog buffer中数据页的变化直接刷新到磁盘中，持久化的保存在磁盘中。如果在脏页刷新时出错了，可以通过 redolog buffer来进行恢复
- 将buffer pool中的数据变化先存储到redolog buffer中，按批次提交，可以减少I/O次数，并且是顺序磁盘I/O，提高效率和性能。这种机制称为WAL(write_ahead logging),即先写日志，然后再刷新到磁盘上。redolog日志会定时清理，两份文件循环写，并不会永久保留下来

##### 3.2 undo log

- 回滚日志，用于记录数据被修改前的信息，作用包含两个：提供**回滚**和**MVCC(多版本并发控制)。**
- undo log和redo log记录物理日志不一样，它是逻辑日志。可以认为当delete一条记录时，undo log中会记录一条对应的insert记录，反之亦然，当update一条记录时，它记录一条对应相反的update记录。当执行rollback时，就可以从undo log中的逻辑记录读取到相应的内容并进行回滚。
  - Undo log销毁：undo log在事务执行时产生，事务提交时，并不会立即删除undo log，因为这些日志可能还用于MVCC。
  - Undo log存储：undo log采用段的方式进行管理和记录，存放在前面介绍的**rollback segment回滚段**中，内部包含1024个undo log segment。



#### 4.MVCC 多版本并发控制

##### 4.1 基本概念

- 当前读
      读取的是记录的最新版本，读取时还要保证其他并发事务不能修改当前记录，会对读取的记录进行加锁。对于我们日常的操作，如：select..., look in share mode（共享锁）, select... for update、update、insert、delete（排他锁）都是一种当前读。

- 快照读
      简单的select(不加锁)就是快照读，快照读，读取的是记录数据的可见版本，有可能是历史数据。这个操作不加锁，是非阻塞读。
  - Read Committed:每次select,都生成一个快照读。
  - Repeatable Read:开启事务后第一个select语句才是快照读的地方。
  - Serializable:快照读会退化为当前读。

- MVCC

  ​    全称 Multi-Version Concurrency Control，多版本并发控制。指**维护一个数据的多个版本，使得读写操作没有冲突**，快照读为MySQL实现MVC提供了一个非阻塞读功能。MVC的具体实现，还需要依赖于数据库记录中的三个隐式字段、undo log日志、readview。

##### 4.2 隐藏字段

|          隐藏字段          |                             含义                             |
| :------------------------: | :----------------------------------------------------------: |
|   db_trx_id 数据库事务id   |         记录插入这条记录或最后一次修改该记录的事务ID         |
| db_roll_ptr 数据库回滚指针 | 回滚指针，指向这条记录的上一个版本，用于配合undo log，指向上一个版本 |
|    db_row_id 隐藏主键id    |     隐藏主键，如果表结构没有指定主键，将会生成该隐藏字段     |

- 使用`ibd2adi stu.ibd`命令来查看ibd文件的内容



##### 4.3 undo log

- 回滚日志.，在insert、update、delete的时候产生的便于数据回滚的日志。
- 当insert的时候，产生的undo log日志只在回滚时需要，在事务提交后，可被立即删除。而update、delete的时候，产生的undo log日志不仅在回滚时需要，在快照读时也需要，不会立即被删除。

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-10 211512.png)

- undo log 版本链：不陶事务或相同事务对同一条记录进行修改，会导致该记录的undolog生成一条记录版本链表，链表的头部是最新的旧记录，链表尾部是最早的旧记录。



##### 4.4 read view

- **Read View（读视图）**是快照读SQL执行时MCC提取数据的依据，记录并维护系统当前活跃的事务（未提交的）id。ReadView中包含了四个核心字段：

|      字段      |                         含义                         |
| :------------: | :--------------------------------------------------: |
|     m_ids      |                 当前活跃的事务ID集合                 |
|   min_trx_id   |                    最小活跃事务ID                    |
|   max_trx_id   | 预分配事务ID，当前最大事务ID+1（因为事务ID是自增的） |
| creator_trx_id |                ReadView创建者的事务ID                |

版本链数据访问规则（trx_id:代表是当前事务ID）

|                条件                 |                是否访问                |                   原因                   |
| :---------------------------------: | :------------------------------------: | :--------------------------------------: |
|     trx_id == creator _ trx_id?     |             可以访问该版本             |    成立，说明数据是当前这个事务更改的    |
|        trx_id < min_trx_id?         |             可以访问该版本             |         成立，说明数据已经提交了         |
|        trx_id > max_trx_id?         |            不可以访问该版本            | 成立，说明该事务是在ReadView生成后才开启 |
| min_trx_id <= trx_id <= max_trx_id? | 如果trx_id不在m_id中是可以访问该版本的 |             说明数据已经提交             |

- 不同的隔离级别，生成ReadView的时机不同：
  - read committed：在事务中每一次执行快照读时生成ReadView。
  - repeatable read：仅在事务中第一次执行快照读时生成ReadView，后续复用该ReadView。

##### 4.5 RC隔离级别

- 在事务中每一次执行快照读时生成ReadView。

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-11 194740.png)

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-11 194936.png)

- 只要有一个条件即可

##### 4.6 RR隔离级别

- 仅在事务中第一次执行快照读时生成ReadView，后续复用该ReadView

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-11 195201.png)![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-11 195326.png)原子性-undo log   持久性-redo log     一致性-undo log+redo log     隔离性-锁+MVCC

### 七、MYSQL管理

#### 1. 系统数据库

- Mysql数据库安装完成后，自带了一下四个数据库，具体作用如下：

|        数据库        |                             含义                             |
| :------------------: | :----------------------------------------------------------: |
|       *mysql*        | 存储MySQL服务器正常运行所需要的各种信息（时区、主从、用户、权限等） |
| *information schema* | 提供了访问数据库元数据的各种表和视图，包含数据库、表、字段类型及访问权限等 |
| *performance_schema* | 为MySQL服务器运行时状态提供了一个底层监控功能，主要用于收集数据库服务器性能参数 |
|        *sys*         | 包含了一系列方便DBA和开发人员利用performance _schema性能数据库进行性能调优和诊断的视图 |

#### 2. 常用工具

##### 2.1 mysql

- 该mysql不是指mysql服务，而是指mysql的客户端工具。

```sql
语法：mysql [options] [database]
选项：
	-u,--user=name #指定用户名
	-p,--password[=name] #指定密码
	-h,--host=name #指定服务器IP或域名
	-p,--port=port #指定连接端口
	-e,--execute=name #执行SQL语句并退出
	
-e选项可以在Mysql客户端执行SQL语句，而不用连接到MySQL数据库再执行，对于一些批处理脚本，这种方式尤其方便。
示例： mysql -uroot -p123456 db01 -e "select* from stu";
```

##### 2.2 mysqladmin

- mysqladmin是一个执行管理操作的客户端程序。可以用它来检查服务器的配置和当前状态、创建并删除数据库等。

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-11 200839.png)

```sql
示例：
mysqladmin -uroot -p123456 drop 'test01';
mysqladmin -uroot -p123456 version;
```

##### 2.3 mysqlbinlog

- 由于服务器生成的二进制日志文件以二进制格式保存，所以如果想要检查这些文本的文本格式，就会使用到mysqlbinlog 日志管理工具。

```sql
语法： mysqlbinlog [options] log-files1 log-files2 ...
选项：
	-d,--database=name	指定数据库名称，只列出指定的数据库相关操作。
	-o,--offset=#	忽略掉日志中的前n行命令。
	-r,--result_file=name	将输出的文本格式日志输出到指定文件。
	--s,--short-form	显示简单格式，省略掉一些信息。
	--start-datatime=date1 --stop-datetime=date2	指定日期间隔内的所有日志。
	--start-position=pos1 --stop-position=pos2	指定位置间隔内的所有日志。
```

##### 2.4 mysqlshow 

- mysqlshow客户端对象查找工具，用来很快地查找存在哪些数据库、数据库中的表、表中的列或者索引。

```sql
语法：mysqlshow [options] [db_name[table_name[col_name]]]
选项：
	--count	显示数据库及表的统计信息（数据库，表均可以不指定）
	-i	显示指定数据库或者指定表的状态信息
示例：

#查询每个数据库的表的数量及表中记录的数量
mysqlshow -uroot -p2143 --count

#查询test库中每个表中的字段书，及行数
mysqlshow -uroot -p2143 test --count

#查询test库中book表的详细情况
mysqlshow -uroot -p2143 testbook --count
```



##### 2.5 mysqldump

- mysqldump客户端上具用来备份数据库或在不同数据库之间进行数据迁移。备份内容包含创建表，及插入表的SQL语句。

```sql
语法：
	mysqldump[options] db_name[tables]
	mysqldump[options] --database/-B db1[db2 db3...]
	mysqldump[options] --all-databases/-A
连接选项：
	-u,--user=name	指定用户名
	-p,--password[=name]	指定密码
	-h,--host=name	指定服务器ip或域名
	--P,--port=#	指定连接端口
输出选项：
	--add-drop-database	
	在每个数据库创建语句前加上drop database语句
	
	--add-drop-table	
	在每个表创建语句前加上drop table语句，默认开启；不开启(--skip-add-drop-table)
	-n,--no-create-db	不包含数据库的创建语句
    -t,--no-create-info	不包含数据表的创建语句
    -d,--no-data	不包含数据
	--T,--tab=name	
	自动生成两个文件：一个.sql文件，创建表结构的语句；一个.txt文件，数据文件
```

##### 2.6 mysqlimport/source

- mysqlimport是客户端数据导入工具，用来导入mysqldmp加-T参数后导出的txt文件

```sql
语法：mysqlimport[options] db_name textfile1[textfile2...]
示例：
mysqlimport -uroot -p2143 test/tmp/city.txt


如果需要导入sql文件，可以使用mysql中的source指令：
语法：source/root/xxxx.sql
```















































