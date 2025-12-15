## 运维篇

### 一、日志

- MySQL的日志系统是数据库管理中至关重要的一部分，主要包括错误日志、查询日志、慢查询日志、事务日志和二进制日志

#### 1.错误日志

- 错误日志是 MySQL 中最重要的日志之一，它记录了当 mysqld 启动和停止时，以及服务器在运行过程中发生任何严重错误时的相关信息。当数据库出现任何故障导致无法正常使用时，建议首先查看此日志。

- 该日志是默认开启的，默认存放目录/var/log/，默认的日志文件名为 mysqld.log。查看日志位置：`show variables like '% log _ error%`

#### 2.二进制日志

##### 2.1 介绍

- 二进制日志（binlog）记录了所有的DDL（数据定义语言）语句和DML（数据操纵语言）语句，但不包括数据查询（SELECT、SHOW）语句

- 作用：①灾难时的数据恢复；②.MySQL的主从复制
- 默认二进制日志是开启的，查询参数：`show variables like '%log_bin%'`

![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-11 210717.png)

##### 2.2 日志格式

- MySQL服务器中提供了多种格式来记录二进制日志，具体格式及特点如下：

| 日志格式  |                             含义                             |
| :-------: | :----------------------------------------------------------: |
| statement | 基于SQL语句的日志记录，记录的是SQL语句，对数据进行修改的SQL都会记录在日志文件中 |
|    row    |      基于行的日志记录，记录的是每一行的数据变更（默认）      |
|   mixed   | 混合了STATEMENT和ROW两种格式，默认采用STATEMENT，在某些特殊情况下会自动切换为ROW进行记录 |

- 查询参数：`show variables like '%binlog_format%';`

<img src="C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-11 211114.png" style="zoom: 80%;" />

##### 2.3 日志查看

- 由于日志是以二进制方式存储的，不能直接读取，需要通过二进制日志查询工具mysqlbinlog来查看，具体语法：

```sql
语法：mysqlbinlog [参数选项] logfilename
参数选项：
	-d	指定数据库名称，只列出指定的数据库相关操作。
	-o	忽略掉日志中的前n行命令。
	-v	将行事件(数据变更)重构为SQL语句
	-w	将行事件(数据变更)重构为SQL语句，并输出注释信息
```

##### 2.4 日志删除

- 对于比较繁忙的业务系统，每天生成的blog数据巨大，如果长时间不清除，将会占用大量磁盘空间。可以通过以下几种方式清理日志：

|                      指令                       |                             含义                             |
| :---------------------------------------------: | :----------------------------------------------------------: |
|                  reset master                   | 删除全部binlog日志，删除之后，日志编号，将从binlog.000001重新开始 |
|       purge master logs to 'binlog.xxxx'        |                  删除xxxx编号之前的所有日志                  |
| purge master log before 'yyyy-mm-dd hh24:ml:ss' |   删除日志时间为‘"yyyy-mm-ddhh24:mi:ss"之前产生的所有日志    |

- 也可以在mysql的配置文件中配置二进制日志的过期时间。设置了之后，二进制日志过期会自动删除。
  参数：`show variables like '%binlog_expire_logs_seconds%';`



#### 3.查询日志

- 查询日志中记录了客户端的所有操作语句，而二进制日志不包含查询数据的SQL语句。默认情况下，查询日志是未开启的。
- 如果需要开启查询日志，可以设置以下配置：`show variables like '%general%';`

![image-20251011212549739](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251011212549739.png)

```sql
修改MySQL的配置文件/etc/my.cnf文件，添加如下内容：

#该选项用来开启查询日志，可选值：0或者1；0代表关闭，1代表开启
general log=1
#设置日志的文件名，如果没有指定，默认的文件名为host_name.log
general_log_file= mysql_query.log

#更改后需要重启服务 systemstl restart mysqld
```



#### 4.慢查询日志

- 慢查询日志记录了所有执行时间超过参数 long_query_time 设置值,并且扫描记录数不小于 min_examined_row_limit 的所有的SQL语句的日志，默认未开启。
- long _query_time 默认为 10 秒，最小为 0，精度可以到微秒。通过修改配置文件来实现

```sql
#慢查询日志
slow_query_log=1

#执行时间参数
long_query_time=2
```

- 默认情况下，不会记录**管理语句，**也不会记录**不使用索引进行查找**的查询。可以使用log_slow_admin_statements和更改此行为log_queries_not_using_indexes,如下所述

```sql
#记录执行较慢的管理语句
log_slow_admin_statements=1

#记录执行较慢的未使用索引的语句
log_queries_not_using_indexes=1
```



### 二、主从复制

#### 1.概述

- 主从复制是指将主数据库的DDL 和 DML 操作通过二进制日志传到从库服务器中，然后在从库上对这些日志重新执行（也叫重做），从而使得从库和主库的数据保持同步
- MySQL支持一台主库同时向多台从库进行复制，从库同时也可以作为其他从服务器的主库，实现链状复制。

![image-20251012160644707](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251012160644707.png)

- MySQL 复制的有点主要包含以下三个方面：
  1. 主库出现问题，可以快速切换到从库提供服务。
  2. 实现读写分离，降低主库的访问压力。
  3. 可以在从库中执行备份，以避免备份期间影响主库服务。



#### 2.原理

![image-20251012173914948](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251012173914948.png)

从上图来看，复制分成三步：

1. Master主库在事务提交时，会把数据变更记录在二进制日志文件Binlog中。
2. 从库读取主库的二进制日志文件Binlog，写入到从库的中继日志Relay Log。
3. slave**重做**中继日志中的事件，将改变反映它自己的数据。



#### 3.搭建

##### 3.1 服务器准备

![image-20251012174222992](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251012174222992.png)

##### 3.2 主库配置

●主库配置
1.修改配置文件/ etc/ my. cnf

```sql
#mysql服务ID，保证整个集群环境中唯一，取值范围：1 - 2^{3 2} - 1 ，默认为1
server-id=1

#是否只读，1代表只读，0代表读写
read-only=0

#忽略的数据，指不需要同步的数据库

# binlog-ignore-db=mysql

#指定同步的数据库

# binlog-do-db=db01
```



### 三、分库分表

#### 1. 介绍

##### 1.1 问题分析

- 随着互联网及移动互联网的发展，应用系统的数据量也是成指数式增长，若采用单数据库进行数据存储，存在以下性能瓶颈：
  - IO瓶颈：热点数据太多，数据库缓存不足，产生大量磁盘IO，效率较低。请求数据太多，带宽不够，网络IO瓶颈。
  - CPU瓶颈：排序、分组、连接查询、聚合统计等SQL会耗费大量的CPU资源，请求数太多，CPU出现瓶颈。
- 分库分表的中心思想都是将数据分散存储，使得单一数据库/表的数据量变小来缓解单一数据库的性能问题，从而达到提升数据库性能的目的。

##### 1.2 拆分策略

![image-20251012180731757](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251012180731757.png)

从维度来讲，分为垂直拆分和水平拆分；从拆分的力度来讲，分为分库和分表

分库，指对一个数据库进行拆分，将一个数据库的数据分散的存储在多个数据库当中

分表：对表结构进行拆分，将原来存储在一张表结构当中的数据分散的存储在多张表结构中

- 垂直拆分：

<img src="C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251012181219040.png" alt="image-20251012181219040" style="zoom: 67%;" />

<img src="C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251012181336576.png" alt="image-20251012181336576" style="zoom:67%;" />

- 水平拆分

<img src="C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251012181448713.png" alt="image-20251012181448713" style="zoom:67%;" />

<img src="C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251012181535505.png" alt="image-20251012181535505" style="zoom:67%;" />



##### 1.3 实现技术

![image-20251012181733790](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251012181733790.png)

- shardingDBC: 基于AOP原理，在应用程序中对本地执行的SQL进行拦截，解析、改写、路由处理。需要自行编码配置实现，只支持java语言，性能较高。

- MyCat：数据库分库分表中间件，不用调整代码即可实现分库分表，支持多种语言，性能不及前者。

（MyCat 被称为数据库分表分库中间件，核心原因是它能在应用与数据库之间搭建一层代理，自动处理分表分库的复杂逻辑，让应用无需感知数据的拆分细节。）



#### 2. Mycat

- Mycat是开源的、活跃的、基于Java语言编写的MySQL数据库中间件。可以像使用mysql一样来使用mycat，对于开发人员来说根本感觉不到mycat的存在。

<img src="C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251012183109939.png" alt="image-20251012183109939" style="zoom:50%;" />



- 优势：
  ·	性能可靠稳定
  ·	强大的技术团队
  ·	体系完善
  ·	社区活跃



![image-20251014122322130](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251014122322130.png)

1. bin:存放可执行文件，用于启动停止mycat
2. conf:存放mycat的配置文件
3. lib:存放mycat的项目依赖包(jar)
4. logs:存放mycat的日志文件



#### 2.mycat概述

![image-20251014123403093](C:\Users\Silence\AppData\Roaming\Typora\typora-user-images\image-20251014123403093.png)

- MyCat 不存储数据
  它是一个数据库分表分库中间件，自身并不具备数据存储能力，主要起到**请求路由、数据分片管理、结果聚合**的作用。数据实际存储在其背后的物理数据库（如 MySQL 等）中，也就是图中底层的 “节点主机” 所对应的数据库实例里。

​	这张图清晰展示了 MyCat 在**分表分库架构**上的核心特点，可从**逻辑层与物理层的分离**、**分片管理的精细化**两个维度分析：

##### 2.1. 逻辑结构与物理结构的解耦

- **逻辑层**：通过`schema`（逻辑库）和`tableA`/`tableB`（逻辑表），为应用提供了 “单库单表” 的逻辑视角，让应用无需感知数据的物理拆分。
- **物理层**：底层由多个`dataNode`（分片节点）对应真实的数据库主机（如`192.168.200.201:3306`等不同 IP 端口的数据库实例），实现了数据的物理分散存储。

##### 2.2 分片规则的中心化管理

- 图中明确体现了**分片规则**的存在：`tableA`和`tableB`通过不同的分片规则，将数据路由到`dataNode1-dataNode3`、`dataNode4-dataNode6`等不同分片节点。
- 这种设计让 MyCat 可以**灵活定义拆分策略**（如哈希、范围、枚举等），并将拆分逻辑集中在中间件层，无需侵入应用代码。

##### 2.3 集群的横向扩展性

每个`dataNode`对应独立的物理数据库主机，当数据量增长时，可通过**新增 dataNode（分片节点）** 实现集群的横向扩展，而应用层只需通过 MyCat 的逻辑库表访问，完全无感知。

这张图直观呈现了 MyCat 作为分库分表中间件的核心价值：**通过逻辑层封装，屏蔽物理层的分片复杂度，同时支持灵活的分片规则和集群扩展**。





### 四、读写分离