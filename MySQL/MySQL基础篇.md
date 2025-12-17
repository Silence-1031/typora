#  Mysql基础

### 一、SQL

#### 1. SQL通用语法

- SQL语句可以多行或单行书写，并以分号 **；**结尾
- SQL语句使用 *空格* 或 *缩进* 来增强语句可读性
- MYSQL 数据库的 SQL 语句不区分大小写，关键字建议大写
- 注释：
  - 单行注释：**--** 或 **#** 
  - 多行注释：/*** 注释内容 ***/

#### 2. SQL 分类

|     DDL      |     DML      |     DQL      |   DCL    |
| :----------: | :----------: | :----------: | :------: |
| 数据定义语言 | 数据操作语言 | 数据查询语言 | 数据语言 |

#### 3. DDL 语句

- DDL 语句全称 Data Definition Language，数据定义语言，用来定义数据库对象，如数据库，表，字段等

##### 3.1 数据库操作

- 查询所有数据库

  ```sql
  show databases;
  ```

  

- 查询当前数据库

  ```sql
  select databses（）;
  ```

  

- 创建数据库

  ```sql
  create database （if not exists）数据库名 （defalut charset 字符集）（collate 排序规则）;
  ```

  

- 删除数据库

  ```sql
  drop database （if exists）数据库名 ;
  ```

  

- 切换数据库

  ```sql
  use 数据库名;
  ```

  

##### 3.2 表操作

- 查询当前数据库所有表

  ```sql
  show tables;
  ```

  

- 查看指定表结构

  ```sql
  desc 表名;
  ```

- 查询制定表的建表语言
  ```sql
  show create table 表名;
  ```
- 创建表结构
```sql
create table 表名(
		字段1 字段1类型（comment 字段1注释），
		字段2 字段2类型（comment 字段2注释），
		...
		字段n 字段n类型（comment 字段n注释）
）（comment 表注释）;
```
##### 3.3 表操作的数据类型
- mysql中的数据类型很多，主要分为三类：数值类型，字符串类型，日期时间类型




 | 字符串类型 |         大小          |             描述             |
  | :--------: | :-------------------: | :--------------------------: |
  |    char    |      0-255 bytes      |          定长字符串          |
  |  VARCHAR   |     0-65535 bytes     |          变长字符串          |
  |  TINYBLOB  |      0-255 bytes      | 不超过255个字符的二进制数据  |
  |  TINYTEXT  |      0-255 bytes      |         短文本字符串         |
  |    BLOB    |    0-65 535 bytes     |    二进制形式的长文本数据    |
  |    TEXT    |    0-65 535 bytes     |          长文本数据          |
  | MEDIUMBLOB |  0-16 777 215 bytes   | 二进制形式的中等长度文本数据 |
  | MEDIUMTEXT |  0-16 777 215 bytes   |       中等长度文本数据       |
  |  LONGBLOB  | 0-4 294 967 295 bytes |   二进制形式的极大文本数据   |
  |  LONGTEXT  | 0-4 294 967 295 bytes |         极大文本数据         |



|   数值类型   |  大小   |                 有符号（signed）范围                  |                  无有符号(unsigned)范围                   |        描述        |
| :----------: | :-----: | :---------------------------------------------------: | :-------------------------------------------------------: | :----------------: |
|   TinyInt    | 1 byte  |                     （-128，127）                     |                        （0，255）                         |      小整数值      |
|   SMALLINT   | 2 bytes |                    (-32768，32767)                    |                        (0，65535)                         |      大整数值      |
|  MEDIUMINT   | 3 bytes |                  (-8388608，8388607)                  |                       (0，16777215)                       |      大整数值      |
| INT或INTEGER | 4 bytes |               (-2147483648，2147483647)               |                      (0，4294967295)                      |      大整数值      |
|    BIGINT    | 8 bytes |                    (-2^63，2^63-1)                    |                        (0，2^64-1)                        |     极大整数值     |
|    FLOAT     | 4 bytes |       (-3.402823466 E+38，3.402823466351 E+38)        |         0 和 (1.175494351 E-38，3.402823466 E+38)         |   单精度浮点数值   |
|    DOUBLE    | 8 bytes | (-1.7976931348623157 E+308，1.7976931348623157 E+308) | 0 和 (2.2250738585072014 E-308，1.7976931348623157 E+308) |   双精度浮点数值   |
|   DECIMAL    |         |              依赖于M(精度)和D(标度)的值               |                依赖于M(精度)和D(标度)的值                 | 小数值(精确定点数) |



| 日期类型  | 大小 |                    范围                    |        格式         |           描述           |
| :-------: | :--: | :----------------------------------------: | :-----------------: | :----------------------: |
|   date    |  3   |          1000-01-01 至 9999-12-31          |     YYYY-MM-DD      |          日期值          |
|   TIME    |  3   |          -838:59:59 至 838:59:59           |      HH:MM:SS       |     时间值或持续时间     |
|   YEAR    |  1   |                1901 至 2155                |        YYYY         |          年份值          |
| DATETIME  |  8   | 1000-01-01 00:00:00 至 9999-12-31 23:59:59 | YYYY-MM-DD HH:MM:SS |     混合日期和时间值     |
| TIMESTAMP |  4   | 1970-01-01 00:00:01 至 2038-01-19 03:14:07 | YYYY-MM-DD HH:MM:SS | 混合日期和时间值，时间戳 |

##### 3.4 表修改
- 添加字段
  ```sql
  alter table 表名 add 字段名 类型（长度）（comment 注释）（约束）；
  ```
  
- 修改数据类型
  ```sql
  alter table 表名 modify 字段名 新数据类型(长度)；
  ```
- 修改字段名和字段类型
  ```sql
  alter table 表名 change 旧字段名 新字段名 类型 （长度）（comment 注释）（约束）；
  ```
- 删除字段
  ```sql
  alter table 表名 drop 字段名；
  ```
- 修改表名
  ```sql
  alter table 表名 rename to 新表名；
  ```
##### 3.6 表删除
- 删除表
  ```sql
  drop table (if exists) 表名；
  ```
- 删除指定表，并重新创建表
  ```sql
  truncate table 表名；
  ```
  **注意**：在删除表的时候，表中数据也会全部删除，*truncate*相当于重新创建了一个空白表

#### 4. DML语句

- DML 全称为 Data Manipulation Language(数据操作语言)，用于对数据库中表的数据记录进行增删改操作

##### 4.1 添加数据 insert

- 给指定字段添加数据

  ```sql 
  insert into 表名 （字段1，字段2，）
- 给全部字段添加数据
  ```sql
  insert into 表名 values （值1，值2，···）；
  ```
- 批量添加数据
  ```sql
  -- 给某些字段批量添加数据
  insert into 表名（字段1，字段2，···）values（值1，值2，···），（值1，值2，···），（值1，值2，···）；
  
  -- 给所有字段添加数据
  insert into 表名 values （值1，值2，···），（值1，值2，···），（值1，值2，···）；
  ```
  **注意**：
  - 插入数据时，字段顺序与值的顺序要一一对应
  - 字符串和日期型数据应该包含在引号中
  - 插入的数据大小，应该在字段的规定范围内
##### 4.2 修改数据 update
- 语法：
  ```sql
  update 表名 set 字段名1=值1，字段名2=值2，···（where 条件）；
  ```

##### 4.3 删除数据 delete
- 语法：
  ```sql
  delete from 表名 （where 条件）；
  ```

***

#### 5. DQL语句

- DQL 全称为 Data Query Language(数据查询语言)，用来查询数据库中表的记录
- 查询关键字：**select**
- 在一个业务系统中，查询频次远远高于增删改，当我们访问网站时，看到的数据都是从数据库中查询并展示的，而在查询的过程中，还会涉及到条件、排序、分页等操作

##### 5.1 基本查询

- 不带任何条件
- 查询多个字段
  ```sql
  select 字段1，字段2，字段3···from 表名；
  select * from 表名；
  
  -- * 代表查询所有字段，但在实际中少用，不直观，影响效率
  ```
- 字段设置别名
  ```sql
  select 字段1 （as 别名），字段2（as 别名）··· from 表名；
  
  -- as也可以省略
  select 字段1 （别名），字段2（别名）··· from 表名；
  ```
- 去除重复记录
  ```sql
  select dinstinct 字段 from 表名；
  ```
##### 5.2 条件查询
- 语法：
  ```sql
  select 字段列表 from 表名 where 条件列表；
  ```
- 条件：
  ｜比较运算符｜功能｜
  ｜逻辑运算符｜功能｜
- 

##### 5.3 聚合函数

- 将一列数据作为一个整体，进行纵向计算

- 常见聚合函数：

  | 函数  |   功能   |
  | :---: | :------: |
  | count | 统计数量 |
  |  max  |  最大值  |
  |  min  |  最小值  |
  |  avg  |  平均值  |
  |  sum  |   求和   |

- 语法：

  ```sql
  select 聚合函数(字段列表) from 表名;
  
  注：所有*null*值不参与**聚合函数**计算

##### 5.4 分组查询

- 语法：

  ```sql
  select 字段列表 from 表名 (where 条件) group by 分组字段名 (having 分组后过滤条件);
  ```

- where与having区别

  - **执行时机不同**：where是分组之前进行过滤，不满足where条件，不参与分组；而having是分组之后对结果进行过滤
  - **判断条件不同**：where不能对聚合函数进行判断，而having可以

- 分组查询与聚合函数通常会一起使用

- **注意**：

  - **执行顺序**: where > 聚合函数> having
  - 分组之后，查询的字段一般为<u>聚合函数和分组字段</u>，查询其他字段无任何意义
  - 支持多字段分组，语法为``group by columnA,columnB``

- 举例：

  ```sql
  -- 分组查询
  -- 1.根据性别分组，统计男性员工和女性员工的数量
  select gender, count(*) from emp group by gender;
  
  -- 2.根据性别分组，统计男性员工和女性员工的平均年龄
  select gender, avg(age) from emp group by gender;
  
  -- 3.查询年龄小于45的员工 ，并根据工作地址分组 ，获取员工数量大于等于3的工作地址
  select workaddress,count(*) address_count from emp where age < 45 group by workaddress having address_count>=3;
  ```

  

##### 5.5 排序查询

- 排序操作在我们日常生活中很常用，比如购物软件中的查找排序

- 语法：

  ```sql
  select 字段列表 from 表名 order by 字段1 排序方式1,字段2 排序方式2;
  ```

  **排序方式**分两种：

  - asc ：升序 (默认值)  可省略
  - desc : 降序

- **注意**：如果是*多字段排序*，当第一个字段值相同时，才会根据第二个字段进行排序。

##### 5.6 分页查询

- 语法
  ```sql
  select  字段列表 from 表名 limit 起始索引,查询记录数；
  ```

- **注意**：
  - 起始索引从 0 开始，起始索引 =（查询页码-1）* 每页显示记录数
  - 分页查询是数据库的方言（数据库间不一样的地方），不同的数据库有不同的实现，MySQL中是limit
  - 如果查询的是第一页数据，起始索引可以省略，直接简写为limit10

***

```sql
-- DQL 语句练习

-- 1.查询年龄为29，21，22，23岁的女性员工信息。
select * from emp where gender="女" and age in(20,21,22,23);

-- 2.查询性别为男，并且年龄在28-46岁（含）以内的姓名为三个字的员工。
select * from emp where gender="男" and(between 20 and 40 )and name like '___';

-- 3.统计员工表中，年龄小于60岁的，男性员工和女性员工的人数。
select count(*) from emp where age <60 group by gender;

-- 4.查询所有年龄小于等于35岁员工的姓名和年龄，并对查询结果按年龄升序排序，如果年龄相同按入职时间降序排序。
select name,age from emp where age<=35 order by age asc,entrydate desc;

-- 5.查询性别为男，且年龄在28-46岁（含）以内的前5个员工信息，对查询的结果按年龄升序排序，年龄相同按入职时间升序排序。
select * from emp where gender="男" and (age between 28 and 46 )order by age asc,entrydate desc limit 0,5;

```

***

##### 5.7 DQL执行顺序

|          编写顺序          | 执行顺序 |
| :------------------------: | :------: |
|   *select*    *字段列表*   |    4     |
|    *from*    *表名列表*    |    1     |
|    *where    条件列表*     |    2     |
| *group by    分组字段列表* |    3     |
| *having   分组后条件列表*  |    5     |
| *order by    排序字段列表* |    6     |
|    *limit    分页参数*     |    7     |

- 在使用别名时要注意，别名是否可以被正常识别使用

#### 6.DCL语句

- DCL，全称 Data Control Language，数据控制语言，用来管理数据库用户，控制数据库访问权限

##### 6.1 管理用户

- 查询用户

  ```sql
  use mysql;
  select * from user;
  ```

  - MySQL当前的用户信息：
    **Host**代表当前用户访问的主机，如果为**localhost**，代表仅能在当前本机访问，不可以远程访问，**User**代表的是访问该数据库的用户名，在mysql中需要通过Host和User来唯一标识一个用户

  ![用户信息](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-09-30 132334.png)

- 创建用户

  ```sql
  create user '用户名'@'主机名' identidied by '密码';
  ```

  *此时还没分配权限，itcast只能在本机查看数据库*

  

- 修改用户密码

  ```sql
  alter user '用户名'@'主机名' identidied with mysql_native_password by '新密码';
  ```

  

- 删除用户

  ```sql
  drop user '用户名'@'主机名';
  ```

- **注意**：
  - 主机名可以使用%通配
  - 这类SQL开发人员操作的比较少，主要是DBA  (Database Administrator-数据库管理员) 使用

##### 5.2 权限控制

- mysql中定义了很多种权限，但是常用的为以下：
  
  |        权限         |        说明        |
  | :-----------------: | :----------------: |
  | *all,allprivileges* |      所有权限      |
  |      *select*       |      查询数据      |
  |      *insert*       |      插入数据      |
  |      *update*       |      修改数据      |
  |      *delete*       |      删除数据      |
  |       *alter*       |       修改表       |
  |       *drop*        | 删除数据库/表/视图 |
  |      *create*       |   创建数据库/表    |
  
- 查询权限
  ```sql
  show grants for '用户名'@'主机名'；
  ```
  
- 授予权限
  ```sql
  grant 权限列表 on 数据库名.表名 to '用户名'@'主机名'；
  -- 可以用 * 来表示进行通配
  ```
  
- 撤销权限
  ```sql
  revoke 权限列表 on 数据库名.表名 from '用户名'@'主机名'；
  ```
  
- **注意**：
  
  - 多个权限之间，使用逗号分隔
  - 授权时，数据库名和表名可以用 * 进行通配，代表所有


***

### 二、函数

- **函数**，是指一段可以直接被另一程序调用的程序或代码。这也就意味着，函数已经给我们提供了，我们要做的就是在合适的场景中调用对应函数来完成需求。
- 聚合函数也是函数的一种
- mysql中的函数分为四类：字符串函数，数值函数，日期函数，流程函数
#### 1. 字符串函数：

|            函数            |                            功能                             |
| :------------------------: | :---------------------------------------------------------: |
|  *concat（s1,s2,···,sn）*  |           字符串拼接，将S1,S2,…Sn拼接成一个字符串           |
|        *lower(str)*        |                   将字符串str全部转为小写                   |
|        *upper(str)*        |                   将字符串str全部转为大写                   |
|     *lpad(str,n,pad)*      |  左填充，用字符串pad对str的左边进行填充，达到n个字符串长度  |
|     *rpad(str,n,pad)*      |  右填充，用字符串pad对str的右边进行填充，达到n个字符串长度  |
|        *trim(str)*         |                 去掉字符串头部和尾部的空格                  |
| *substring(str,start,len)* | 返回从字符串str从start位置起的len个长度的字符串,下标从1开始 |



#### 2.数值函数

|     函数     |               功能               |
| :----------: | :------------------------------: |
|  *cell(x)*   |             向上取整             |
|  *floor(x)*  |             向下取整             |
|   *mod(x)*   |           返回x/y的模            |
|  *rand( )*   |         返回0~1的随机数          |
| *round(x,y)* | 求参数x的四舍五入值，保留y位小数 |

```sql
-- 通过数据库的函数，生成一个六位数的随机验证码

select lpad(round(rand()*100000,0),6,'0');

```



#### 3.日期函数

|                函数                 |                             功能                             |
| :---------------------------------: | :----------------------------------------------------------: |
|            *curdate( )*             |                         返回当前日期                         |
|            *curtime( )*             |                         返回当前时间                         |
|              *now( )*               |                      返回当前日期和时间                      |
|            *year(date)*             |                      获取指定date的年份                      |
|            *month(date)*            |                      获取指定date的月份                      |
|             *day(date)*             |                      获取指定date的日期                      |
| *date_add(date,interval expr type)* |      返回一个日期/时间值加上一个时间间隔expr后的时间值       |
|       *datediff(date1,date2)*       | 返回起始时间date1和结束时间date2之间的天数<br>用date1-date2，结果有正负 |

```sql
-- 案例：查询所有员工的入职天数，并根据入职天数倒序排序。
select name,datediff(curdate(),entrydate) as 'entrydays' from emp order by entrydays desc;
```



#### 4.流程函数

- 流程函数也是很常用的一个函数，可以在SQL语句中实现条件筛选，从而提高语句的效率

|                             函数                             |                          功能                          |
| :----------------------------------------------------------: | :----------------------------------------------------: |
|                       *if(value,t,f)*                        |          如果value为true, 则返回t, 否则返回f           |
|                   *ifnull(value1,value2)*                    |      如果value1不为空，返回value1, 否则返回value2      |
|     *case when (val1) then (res1) ··· else(default) end*     |   如果val1为true, 返回Ires1,……否则返回default默认值    |
| *case (expr) when (val1) then (res1) ··· else (default) end* | 如果expr的值等于val1, 返回res1,……否则返回default默认值 |



```sql
-- 需求：查询emp表的员工姓名和工作地址（北京/上海 --> 一线城市，其他 --> 二线城市）

select
    name,
    (case workaddress when '北京' then '一线城市' when '上海' then '一线城市' else '二线城市' end) as '工作地址'
from emp;

/* 案例：统计班级各个学员的成绩，展示的规则如下：
>=85，展示优秀
>=60，展示及格
否则，展示不及格
*/

create table score(
        id int comment 'ID',
        name varchar(20) comment '姓名',
        math int comment '数学',
        english int comment '英语',
        chinese int comment '语文'
) comment '学员成绩表';

insert into score(id, name, math, english, chinese)VALUES (1,'Tom', 67, 88, 95), (2,'Rose', 23, 66, 90), (3,'Jack', 56, 98, 76);

select
    id,
    name,
    (case when math>=85 then '优秀' when math>=60 then '及格' else '不及格' end) '数学',
    (case when score.english>=85 then '优秀' when english>=60 then '及格' else '不及格' end) '英语',
    (case when chinese>=85 then '优秀' when chinese>=60 then '及格' else '不及格' end) '语文'
from score;
```





### 三、约束

#### 1. 概述
- 概念：约束是作用于表中字段上的规则，用于限制存储在表中的数据

- 目的：保证数据库中数据的正确性，有效性，完整性

- 分类：
  
  |   约束   |                          概念                          |    关键字     |
  | :------: | :----------------------------------------------------: | :-----------: |
  | 非空约束 |              限制该字段的数据不能为*null*              |  *not null*   |
  | 唯一约束 |           保证该字段数据都是唯一的，不重复的           |   *unique*    |
  | 主键约束 |        主键是一行数据的唯一标识，要求非空且唯一        | *primary key* |
  | 默认约束 |     保存数据时，如果未指定该字段的值，则采用默认值     |   *default*   |
  | 检查约束 |                保证字段值满足某一个条件                |    *check*    |
  | 外键约束 | 用来让两张表的数据间建立联系，保证数据的一致性和完整性 | *foreign key* |
  
  **注意**：
  
  - 约束是作用于表中字段上的，可以在创建/修改表的时候添加约束
  - 对于一个字段，可以有多个约束
  
  

#### 2. 外键约束

- 外键：用来让两张表的数据之间建立连接，从而保证数据的一致性和完整性

![主从表关系](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-01 123033.png)	员工表的外键关联了部门表的主键，让两张表产生连接。这种主外键关系之间，外键所在表为子表，主键所在表为父表。

​	**注意**：目前上述的两张表，在数据库层面，并未建立外键关联，只在逻辑上有关系，所以是无法保证数据的一致性和完整性的，需要在表中创建相应的外键和主键使数据相关联



- 添加外键

  ```sql
  create table 表名（
  		字段名 数据类型，
  		···
  		（constraint） （外键名称） foreign key （外键字段名） references 主表 （主表列名）
  		）；
  
  alter table 表名 add constraint 外键名称 foreign key （外键字段名）references 主表 （主表列名）；
  ```

- 删除外键：
  ```sql
  alter table 表名 drop foreign key 外键名称；
  ```

- 举例：

  ```sql
  -- 添加外键
  alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id);
  
  -- 删除外键
  alter table emp drop foreign key fk_emp_dept_id;
  ```

  

#### 3. 删除/更新行为

- 添加了外键后，再删除父表数据时产生的约束行为，称删除/更新行为，具体分以下几种
  |        行为         |                             说明                             |
  | :-----------------: | :----------------------------------------------------------: |
  | *no action*（默认） | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新（与*restrict*一致) |
  |     *restrict*      | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新（与*no action*一致） |
  |  *cascade*（级联）  | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有，则也删除/更新外键在子表中的记录。 |
  |     *set null*      | 当在父表中删除对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为null（这就要求该外键允许取*null*） |
  |    *set default*    | 父表有变更时，子表将外键列设置成一个默认的值（*Innodb*不支持） |
  
- 语法：
  ```sql
  alter table 表名 add constraint 外键名称 foreign key （外键名称） references 主表名 （主表字段名）on update cascade on delete cascade;
  
  -- 举例：
  alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id) on update cascade on delete cascade;
  
  alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id) on update set null on delete cascade set null;
  ```





   



### 四、多表查询

#### 1. 多表关系：

- 项目开发中，在进行数据库表结构设计时，会根据业务需求及业务模块之间的关系，分析并设计表结构。由于业务之间相互关联，所以各个表结构之间也存在着各种联系，基本分为3种：
  - 一对多/多对一
  - 多对多
  - 一对一

##### 1.1 一对多

- 如：部门与员工之间的关系，一个部门对应多个员工，一个员工对应一个部门

- 实现：在多的一方建立外键，指向一对应的主键

  ![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-01 133848.png)

##### 1.2 多对多

- 如：学生与课程之间的关系，一个学生可以选修多门课，一门课程也可以供多个学生选择

- 实现：建立第三张中间表，中间表至少包含两个外键，分别关联两方主键

  ![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-01 134131.png)

  ```sql
  create table student(
  		id int auto_increment primary key comment '主键ID',
  		name varchar(10) comment '姓名',
  		no varchar(10) comment '学号'
  ) comment '学生表';
  
  insert into student values(null,'呼唤','2000100101'),(null, '谢逊', '2000100102'),(null, '殷天正', '2000100103');
  
  create table course(
  		id int auto_increment primary key comment '主键ID',
  		name varchar(10) comment '课程名称'
  ) comment '课程表';
  
  insert into course values (null, ' Java'), (null, 'PHP'), (null, 'MySQL') , (null, 'Hadoop');
  
  create table student_course(
      id int auto_increment comment '主键' primary key,
      studentid int not null,
      courseid int not null,
      constraint fk_course_id foreign key (courseid) references course(id),
      constraint fk_student_id foreign key (studentid) references student(id)
  );
  
  insert into student_course values(null,1,1),(null,1,2),(null,1,3),(null,2,2),(null,2,3),(null,3,4);
  
  ```

  ![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-01 135846.png)

##### 1.3 一对一

- 如：用户与用户详情之间的关系，一对一关系，多用于单表拆分，将一张表的基础字段放在一张表中，其他详情字段放在另一张表中，以提升操作效率

- 实现：在任意一方加入外键，关联另一方的主键，并且设置外键为唯一（unique）

  ![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-01 140507.png)

  ```sql
  create table tb_user(
  		id int auto_increment primary key comment '主键]ID',
      	name varchar(10) comment '姓名',
  		age int comment '年龄',
  		gender char(1) comment '1: 男,2: 女',
  		phone char(11) comment '手机号'
  )comment '用户基本信息表';
  
  create table tb_user_edu(
  		id int auto_INCREMENT primary key comment'学历',
  		degree varchar(26) comment '学历',
  		major varchar(58) comment'专业',
  		primaryschool varchar(58) comment'小学',
  		middleschool varchar(58) comment'大学',
  		userid int unique comment'用户ID',  -- 外键
  		constraint fk_userid foreign key(userid) references tb_user(id)
  ) comment '用户教育信息表';
  
  insert into tb_user(id, name, age, gender, phone) values
  	(null,'黄浩',45,'1','18800001111'),
  	(null,'冰冰',35,'2','18800002222'),
  	(null,'码云',55,'1','18800008888'),
  	(null,'李彦宏',58,'1','18800009999');
  
  insert into tb_user_edu(id, degree, major, primaryschool, middleschool,  userid) values
  	(null,'本科','舞蹈','静安区第一小学','静安区第一中学',1),
  	(null,'硕士','表演','朝阳区第一小学','朝阳区第一中学',2),
  	(null,'本科','英语','杭州市第一小学','杭州市第一中学',3),
  	(null,'本科','应用数学','阳泉第一小学','阳泉区第一中学',4);
  
  ```

  

#### 2. 概述

- 多表查询 即从多张表中查询数据

- 在SQL中进行多表查询时，并不一定需要外键约束。外键约束是一种用于维护数据完整性和一致性的机制，但它并不是进行多表查询的必要条件。通过使用内连接、外连接和子查询等技术，可以在没有外键约束的情况下实现多表查询

- 笛卡尔积：数学中，两个集合A，B的所有组合情况，在多表查询时，需要**消除无效的笛卡尔积**

  ```sql
  select * from emp,dept where emp.dept_id=dept.id;
  -- 通过外键，只查询相关联的数据，从而消除笛卡尔积
  ```

- 分类
  - 连接查询
    - 内连接
    - 外连接
    - 自连接
  - 子查询

#### 3. 内连接

- 内连接（inner join）是多表查询中一种常用的查询方式，它用于根据指定的连接条件，从两个或多个表中检索出匹配的行。内连接只返回两个表中关联字段相匹配的记录，即两个表的**交集部分**

- 查询语法：

  ```sql
  -- 隐式内连接
  select 字段列表 from 表1,表2 where 条件 ···;
  
  -- 显式内连接
  select 字段列表 from 表1 (inner) join 表2 on 连接条件 ···;
  ```

  ```sql
  -- 1. 查询每一个员工的姓名，及关联的部门的名称（隐式内连接实现）
  -- 表结构：emp，dept
  -- 连接条件：emp.dept_id=dept_id
  
  select emp.name,dept.name from emp,dept where emp.dept_id=dept.id;
  select e.name,d.name from emp e ,dept d where e.dept_id=d.id; 
  -- 利用别名简化，但是如果使用了别名，后面就不能再通过原名来对表进行操作
  
  
  -- 2. 查询每一个员工的姓名，及关联的部门的名称（显式内连接实现）
  -- 表结构：emp，dept
  -- 连接条件：emp.dept_id=dept_id
  
  select e.name,d.name from emp e inner join dept d on e.dept_id=d.id;
  select e.name,d.name from emp e join dept d on e.dept_id=d.id;
  -- inner关键字也可省略
  ```

  

#### 4. 外连接

- 外连接是一种**多表查询**的方式，它允许在两个表之间建立一种特殊的连接。这种连接不仅会返回两个表中匹配的行，还会返回其中一个表中没有与之匹配的行。分为左外连接和右外连接两种

  ![](C:\Users\Silence\OneDrive\Pictures\Screenshots\屏幕截图 2025-10-03 115642.png)

- 左外连接：左外连接会返回左表（from 子句中的第一个表）的**所有行**，即使在右表（join子句中指定的表）中没有匹配的行。如果右表中没有匹配的行，则结果集中这部分的右表列会显示为NULL。相当于查询表1(左表)的所有数据，包含表1和表2交集部分的数据

  ```sql
  select 字段列表 from 表1 left (outer) join 表2 on 条件 ···;
  ```

  

- 右外连接：相当于查询表2(右表)的所有数据，包含表1和表2交集部分的数据

  ```sql
  select 字段列表 from 表1 right (outer) join 表2 on 条件 ···;
  ```

```sql
-- 外连接演示
-- 1.查询emp表的所有数据，和对应的部门信息(左外连接)
-- 表结构：emp，dept
-- 连接条件：emp.dept_id=dept.id
select e.*,d.name from emp e left outer join dept d on e.dept_id=d.id;

-- 2.查询dept表的所有数据，和对应的员工信息（右外连接）
select d.*,e.* from emp e right outer join dept d on e.dept_id=d.id;

```





#### 5. 自连接

- **自连接**是在同一张表中进行的连接操作，它允许表与自身进行连接，以解决需要比较同一表中两行数据的问题。在自连接中，表会被视为两个独立的表，通常通过为表指定不同的别名来区分

- 语法：

  ```sql
  select 字段列表 from 表A 别名 A join 表A 别名B on 条件;
  ```

  自连接查询，可以是内连接查询，也可以是外连接查询

  ```sql
  -- 自连接
  -- 1. 查询员工及其所属领导的名字(内连接)
  -- 表结构：emp
  select a.name,b.name from emp a,emp b where a.managerid=b.id;
  -- 自连接必须起别名来使用
  
  -- 2. 查询所有员工 emp 及其领导的名字 emp，如果员工没有领导，也需要查询出来(左外连接)
  select a.name,b.name from emp a left outer join emp b on a.managerid=b.id;
  ```

- 对于联合查询，多张表的列数必须保持一致，字段类型也需要保持一致

#### 6. 联合查询

- union查询，就是把多次查询的结果合并起来，形成一个新的查询结果集。union 会对合并后的结果集进行去重操作，确保返回的每一行都是唯一的。union all不会去除重复记录，而是直接将所有查询结果合并。

  ```sql
  select 字段列表 from 表A ···
  union （all）
  select 字段列表 from 表B ···;
  
  
  eg:
  select * from emp where salary < 5000
  union
  select * from emp where age>50;
  ```

#### 7. 子查询

- SQL语句中嵌套SELECT语句，称为嵌套查询，又称子查询

  ```sql
  select * from t1 where column1 = (select column1 from t2)
  ```

  子查询外部的语句可以是insert/update/delete/select的任意一个

- 根据子查询结果不同，分为：

  - 标量子查询：子查询返回的结果是单个值（数字、字符串、日期等），最简单的形式

    ​			常用的操作符： = / <>/ > /  >=  /< /<=

    ```sql
    -- 标量子查询
    -- 1.查询“销售部”的所有员工信息
    -- a.查询“销售部”部门ID
    select id from dept where name = '销售部';
    -- b.根据销售部部门ID，查询员工信息
    select * from emp where dept_id = 4;
    
    select * from emp where dept_id = (select id from dept where name = '销售部');
    
    -- 2.查询在“方东白”入职之后的员工信息
    -- a.查询方东白的入职日期
    select entrydate from emp where name = '方东白';
    -- b.查询指定入职日期之后入职的员工信息
    select * from emp where entrydate > '2009-02-12';
    
    select * from emp where entrydate > (select entrydate from emp where name = '方东白');
    ```

    

  - 列子查询：子查询返回的结果是一列（可以是多行）

    |  操作符  |                  描述                  |
    | :------: | :------------------------------------: |
    |   *in*   |      在指定的集合范围之内，多选一      |
    | *not in* |         不在指定的集合范围之内         |
    |  *any*   |  子查询返回列表中，有任意一个满足即可  |
    |  *some*  | 与any等同，使用some的地方都可以使用any |
    |  *all*   |    子查询返回列表的所有值都必须满足    |

    ```sql
    -- 列子查询
    -- 1.查询“销售部”和“市场部”的所有员工信息
    -- a.查询“销售部”和“市场部”的部门ID
    select id from dept where name = '销售部' or name = '市场部';
    -- b.根据部门ID，查询员工信息
    select * from emp where dept_id in (2,4);
    -->
    select * from emp where dept_id in (select id from dept where name = '销售部' or name = '市场部');
    
    
    -- 2.查询比财务部所有人工资都高的员工信息
    -- a.查询所有财务部人员工资
    select id from dept where name = '财务部';
    select salary from emp where dept_id = (select id from dept where name = '财务部');
    -- b.比财务部所有人工资都高的员工信息
    select * from emp where salary > all (select salary from emp where dept_id = (select id from dept where name = '财务部'));
    
    -- 3.查询比研发部其中任意一人工资高的员工信息
    -- a.查询研发部所有人工资
    select salary from emp where dept_id = (select id from dept where name = '研发部');
    -- b.比研发部其中任意一人工资高的员工信息
    select * from emp where salary > any (select salary from emp where dept_id = (select id from dept where name = '研发部'));
    ```

    

  - 行子查询：子查询返回的结果是一行（可以是多列)，常用的操作符：=、<>、IN、NOT IN

    ```sql
    -- 行子查询
    -- 1.查询与“张无忌”的薪资及直属领导相同的员工信息；
    -- a.查询“张无忌”的薪资及直属领导
    select salary, managerid from emp where name = '张无忌';
    -- b.查询与“张无忌”的薪资及直属领导相同的员工信息；
    select * from emp where (salary, managerid) = (12500,1); -- 另一种写法
    
    select * from emp where (salary, managerid) = (select salary, managerid from emp where name = '张无忌');
    ```

    

  - 表子查询：子查询返回的结果是多行多列，类似于一张表，常用操作符：in。常用于from后，与其他表进行联合查询

    ```sql
    -- 表子查询
    -- 1.查询与“鹿杖客”，“宋运桥”的职位和薪资相同的员工信息
    -- a.查询“鹿杖客”，“宋运桥”的职位和薪资
    select job, salary from emp where name = '鹿杖客' or name = '宋远桥';
    -- b.查询与“鹿杖客”，“宋运桥”的职位和薪资相同的员工信息
    select * from emp where (job,salary) in (select job, salary from emp where name = '鹿杖客' or name = '宋远桥');
    
    -- 2.查询入职日期是 “2006-01-01”之后的员工信息 ，及其部门信息
    -- a. 入职日期是 "2006-01-01" 之后的员工信息
    select * from emp where entrydate > '2006-01-01';
    -- b.查询这部分员工，对应的部门信息；
    select e.*, d.* from (select * from emp where entrydate > '2006-01-01') e left join dept d on e. dept_id = d. id ;
    ```

    

- 根据子查询位置，分为：where之后、from之后、select之后

  

#### 8.多表查询案例

```sql
-- 1. 查询员工的姓名、年龄、职位、部门信息 （隐式内连接）
-- 表: emp , dept
-- 连接条件: emp.dept_id = dept.id

select e.name , e.age , e.job , d.name from emp e , dept d where e.dept_id = d.id;


-- 2. 查询年龄小于30岁的员工的姓名、年龄、职位、部门信息（显式内连接）
-- 表: emp , dept
-- 连接条件: emp.dept_id = dept.id

select e.name , e.age , e.job , d.name from emp e inner join dept d on e.dept_id = d.id where e.age < 30;


-- 3. 查询拥有员工的部门ID、部门名称
-- 表: emp , dept
-- 连接条件: emp.dept_id = dept.id

select distinct d.id , d.name from emp e , dept d where e.dept_id = d.id;



-- 4. 查询所有年龄大于40岁的员工, 及其归属的部门名称; 如果员工没有分配部门, 也需要展示出来
-- 表: emp , dept
-- 连接条件: emp.dept_id = dept.id
-- 外连接

select e.*, d.name from emp e left join dept d on e.dept_id = d.id where e.age > 40 ;


-- 5. 查询所有员工的工资等级
-- 表: emp , salgrade
-- 连接条件 : emp.salary >= salgrade.losal and emp.salary <= salgrade.hisal

select e.* , s.grade , s.losal, s.hisal from emp e , salgrade s where e.salary >= s.losal and e.salary <= s.hisal;

select e.* , s.grade , s.losal, s.hisal from emp e , salgrade s where e.salary between s.losal and s.hisal;

/*WHERE 条件会对参与查询的每一行数据进行判断，具体到这个多表查询中，判断逻辑如下：
1. 先产生 “临时组合表”
多表查询中，from emp e, salgrade s 会先对两张表进行笛卡尔积操作（即 “交叉连接”），生成一张临时表，临时表的每一行是 emp 表的一条员工记录与 salgrade 表的一条等级记录的组合。
例如：如果 emp 有 10 条员工记录，salgrade 有 5 个等级，临时表会有 10×5=50 行数据。
2. WHERE 条件逐行筛选
WHERE e.salary >= s.losal and e.salary <= s.hisal 会对临时表中的每一行进行判断：
检查当前组合行中，员工的工资是否落在该等级的 [losal, hisal] 范围内。
如果满足条件，这一行会被保留到结果中；如果不满足，则被过滤掉。*/


-- 6. 查询 "研发部" 所有员工的信息及 工资等级
-- 表: emp , salgrade , dept
-- 连接条件 : emp.salary between salgrade.losal and salgrade.hisal , emp.dept_id = dept.id (n张表至少有n-1个连接条件)
-- 查询条件 : dept.name = '研发部'

select e.* , s.grade from emp e , dept d , salgrade s where e.dept_id = d.id and ( e.salary between s.losal and s.hisal ) and d.name = '研发部';

select e.*, s.grade
from emp e,
     dept d,
     salgrade s
where (e.salary >= s.losal and e.salary <= s.hisal)
  and e.dept_id = d.id
  and d.name = '研发部'; -- 格式化的写法

-- 7. 查询 "研发部" 员工的平均工资
-- 表: emp , dept
-- 连接条件 :  emp.dept_id = dept.id

select avg(e.salary) from emp e, dept d where e.dept_id = d.id and d.name = '研发部';



-- 8. 查询工资比 "灭绝" 高的员工信息。
-- a. 查询 "灭绝" 的薪资
select salary from emp where name = '灭绝';

-- b. 查询比她工资高的员工数据
select * from emp where salary > ( select salary from emp where name = '灭绝' );


-- 9. 查询比平均薪资高的员工信息
-- a. 查询员工的平均薪资
select avg(salary) from emp;

-- b. 查询比平均薪资高的员工信息
select * from emp where salary > ( select avg(salary) from emp );


-- 10. 查询低于本部门平均工资的员工信息

-- a. 查询指定部门平均薪资  1
select avg(e1.salary) from emp e1 where e1.dept_id = 1;
select avg(e1.salary) from emp e1 where e1.dept_id = 2;

-- b. 查询低于本部门平均工资的员工信息
select * from emp e2 where e2.salary < ( select avg(e1.salary) from emp e1 where e1.dept_id = e2.dept_id );


-- 11. 查询所有的部门信息, 并统计部门的员工人数
select d.id, d.name , ( select count(*) from emp e where e.dept_id = d.id ) '人数' from dept d;

select count(*) from emp where dept_id = 1;


-- 12. 查询所有学生的选课情况, 展示出学生名称, 学号, 课程名称
-- 表: student , course , student_course
-- 连接条件: student.id = student_course.studentid , course.id = student_course.courseid

select s.name , s.no , c.name from student s , student_course sc , course c where s.id = sc.studentid and sc.courseid = c.id ;
```







### 五、事务

- 事务：是一组操作的集合，它是一个不可分割的工作单位，事务会把所有的操作**作为一个整体**一起向系统提交或撤销操作请求，这些操作要么同时成功，要么同时失败
- 默认MySQL的事务是自动提交的，也就是说，当执行一条DML语句，MySQL会立即隐式的提交事务

#### 1. 事务操作

##### 1.1 查看/设置事务提交方式（方式一）

```sql
select @@autocommit;
set @@autocommit = 0;

-- autocommit = 0为手动提交，=1为自动提交
```

##### 1.2 提交事务

```sql
commit;
```

#####  1.3 回滚事务

```sql
rollback;
```

​	在事务执行的过程中，如果没有报错，那么执行commit提交事务，如果报错，执行rollback回滚事务，将已经执行的语句撤回，这样就保证了数据库中数据的一致性和正确性。

```sql
select @@autocommit;
set @@autocommit=1;

-- 转账操作（张三给李四转账1000）
-- 1. 查询张三账户余额
select * from account where name = '张三';
-- 2. 将张三账户余额-1000
update account set money = money - 1000 where name = '张三';
-- 3. 将李四账户余额+1000
update account set money = money + 1000 where name = '李四';


commit;
```



##### 1.4 开启事务（方式二）

```sql
start transaction;

-- 或者
begin;
```

​	同样，使用commit和rollback来控制事务的提交或回滚

#### 2.事务四大特性（ACID）

- 原子性（*atomicity*）：事务是**不可分割**的最小操作单元，要么全部成功，要么全部失败
- 一致性（*consistency*）：事务完成时，必须使所有数据都保持一致状态
- 隔离性（*isolation*）：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行
- 持久性（*durablity*）：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的

#### 3. 并发事务问题

|    问题    |                             描述                             |
| :--------: | :----------------------------------------------------------: |
|    脏读    |           一个事务读到另外一个事务还没有提交的数据           |
| 不可重复读 | 一个事务先后读取同一条记录，但两次读取的数据不同，称之为不可重复读 |
|    幻读    | 一个事务按照条件查询数据时，没有对应的数据行，但是在插入数据时，又发现这行数据已经存在，好像出现了”幻影” |



#### 4.事务隔离级别

|                  隔离级别                   | 脏读 | 不可重复读 | 幻读 |
| :-----------------------------------------: | :--: | :--------: | :--: |
|       *read uncommitted（读未提交）*        |  ✓   |     ✓      |  ✓   |
|         *read commited（读已提交）*         |  ✕   |     ✓      |  ✓   |
| *repeatable read  (可重复读 )（MySQL默认）* |  ✕   |     ✕      |  ✓   |
|          *serializable（串行化）*           |  ✕   |     ✕      |  ✕   |

隔离级别越高，数据越安全，但是数据库性能越差



##### 4.1 查看事务隔离级别

```sql
select @@transaction_isolation;
```

##### 4.2 设置事务隔离级别

```sql
set (session|global) transaction isolation level (read uncommitted|read committed|repeatable read|serializable);
-- session 会话级别，仅针对当前客户端窗口有效
-- global 全局级别，针对于所有客户端的会话窗口有效
```

