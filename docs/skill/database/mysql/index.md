---
id: mysql-note
slug: /skill/database/mysql
title: MySQL基础
date: 2020-12-30
tags: [mysql, database]
keywords: [mysql, database]
---

## 一、简单操作数据库

### 1.1 创建数据库

```sql
CREATE DATABASE IF NOT EXISTS test;
```

### 1.2 删除数据库

```sql
DROP DATABASE IF EXISTS test
```

### 1.3 使用数据库

```sql
USE test
```

### 1.4 查看数据库

```sql
SHOW DATABASES --查看所有的数据库
```

## 二、数据库的数据类型

### 2.1 数值

| 类型      | 描述                | 所占字节     | 用途                            |
| --------- | ------------------- | ------------ | ------------------------------- |
| tinyint   | 十分小的数据        | 1 个字节     | 一般用来当布尔值用              |
| smallint  | 较小的数据          | 2 个字节     | 少用                            |
| mediumint | 中等的数据          | 3 个字节     | 少用                            |
| **int**   | **标准整数**        | **4 个字节** | **常用，一般都用 int**          |
| bigint    | 较大的整数          | 8 个字节     | 少用                            |
| float     | 单浮点数/单精度小数 | 4 个字节     | 少用                            |
| double    | 双浮点数/双精度小数 | 4 个字节     | 少用 有精度问题                 |
| decimal   | 字符串形式的浮点数  | 不一定       | 精度要求高用 decimal (金融计算) |

### 2.2 字符串

| 类型        | 描述                       | 用途                    |
| ----------- | -------------------------- | ----------------------- |
| char        | 固定大小 0~255，不可变长度 | 存手机号等固定长度      |
| **varchar** | **可变字符串 0~65535**     | **存可变字符串 存变量** |
| tinytext    | 微型文本 2^8-1             | 能用 text 就别用这个    |
| **text**    | **文本串 2^16-1**          | **保存大文本**          |

### 2.3 时间日期

| 类型         | 描述                                       | 用途                 |
| ------------ | ------------------------------------------ | -------------------- |
| date         | YYYY-MM-DD 日期                            | 存日期               |
| time         | HH:mm:ss 时间                              | 存                   |
| **datetime** | **YYYY-MM-DD HH:mm:ss**                    | **最常用的时间格式** |
| timestamp    | 时间戳形式 1970.1.1 8:00:00 到现在的毫秒数 | 但会有 2038 年问题   |

### 2.4 NULL

不要用 NULL 进行运算，结果为 NULL

### 2.5 字段类型

| 字段类似 | 描述                                        | 用途                   |     |
| -------- | ------------------------------------------- | ---------------------- | --- |
| Unsigned | 无符号整数                                  | 该列不能声明为负数     |     |
| zerofill | 用 0 填充                                   | 不足的位数 用 0 来填充 |     |
| 自增     | 自动在上一条记录+1 （默认，可设置自增大小） | 设置唯一的主键 如 id   |     |
| 非空     | not null                                    | 该字段不能为 NULL      |     |
| 默认     | 默认值                                      | 不指定 则默认值        |     |


## 三、操作表

**表名与字段，尽量用``括起来（你永远不知道，你的字段名会不会和关键字重名！）**

### 3.1 创建表

```sql
create table test.t_sys_user
(
    id              int auto_increment comment '主键',
    version         int          default 0                 not null comment '乐观锁',
    user_id         varchar(32)                            not null comment '用户唯一编号',
    username        varchar(64)                            not null comment '用户名称',
    user_account    varchar(64)                            not null comment '用户登录账号',
    password        varchar(128)                           not null comment '密码',
    email           varchar(32)                            not null comment '邮箱',
    phone           varchar(32)  default ''                not null comment '手机号码',
    user_type       varchar(2)                             not null comment '用户类别：内部员工、外部用户',
    organization_id int                                    not null comment '所属机构',
    gender          varchar(2)                             not null comment '用户性别 1:男 0:女',
    status          varchar(2)                             not null comment '用户状态',
    remarks         varchar(512) default ''                not null comment '备注',
    create_time     datetime     default CURRENT_TIMESTAMP not null comment '创建时间',
    create_by       varchar(128)                           not null comment '创建人',
    update_time     datetime     default (now())           null on update CURRENT_TIMESTAMP comment '更新时间',
    update_by       varchar(128) default ''                not null comment '更新人',
    PRIMARY KEY (id),
    KEY `idx_create_time` (`create_time`),
    KEY `idx_username` (`username`),
    unique `idx_unique_email` (`email`),
    unique `idx_unique_user_account` (`user_account`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='用户表';
```

通过上面的手动通过 sql 语句创建表，对已创建的表可通过

- `SHOW CREATE DATABASE 数据库名` 查看数据库的定义语句，也就是输出创建数据库的 sql 语句

- `SHOW CREATE TABLE 表名` 查看表的定义语句，也就是输出创建表的 sql 语句
- `DESC 表名` –显示表的结构 （desc 是 describe 的缩写）

### 3.2 修改表

```sql
 --将表名user修改为account
ALTER TABLE t_sys_user RENAME AS account
--添加字段 age
ALTER TABLE t_sys_user ADD age INT(10)
--修改字段 (修改类型与约束)
ALTER TABLE t_sys_user MODIFY age VARCHAR(10)
--修改字段 (修改字段名)
ALTER TABLE t_sys_user CHANGE age age1 INT(10)
--删除字段
ALTER TABLE t_sys_user DROP age
```

### 3.3 删除表

```sql
DROP TABLE IF RXISTS t_sys_user
```

## 四、MySQL 数据管理

### 4.1 DML 语言

数据库意义：数据存储，数据管理

DML：数据库操作语言

- INSERT
- UPDATE
- DELETE

#### 4.1.1 添加（insert）

```sql
--语法
INSERT INTO 表名([字段1,字段2,字段3]) VALUES ('值1'),('值2'),('值3');

-- 插入数据
INSERT INTO `表名`(`字段`) VALUES ('X');

-- 插入多个数据
INSERT INTO `表名`(`字段`) VALUES ('X'),('X'),('X');
```

#### 4.1.2 更新（update）

**注: 更新一定要带条件，不然就是所有数据都会更新！**

```sql
--语法
UPDATE 表名 SET `字段1`='值1' WHERE 条件1

-- 修改用户名与命名
UPDATE `表明` SET `字段A`='XXXXX',`字段B`='XXXXX` WHERE id = 1
```

#### 4.1.3 删除（delete）

##### DELETE 命令

```sql
--删除数据 条件
DELETE FROM `t_sys_user` WHERE id = 1;
--删除全部数据
DELETE FROM `t_sys_user`
```

##### TRUNCATE 命令（要全部删除用这个命令）

作用：完全清空一个数据库表，表的结构和索引约束不会变。

使用：`TRUNCATE 表名` 即可

好处：删除后，会刷新自增值（置为 0），而 DELETE 不影响自增值，为上一自增值

### 4.2 DQL 语言

（Data Query Language：数据查询语言）

数据库中最核心的语言，使用频率最高的语句。

#### 4.2.1 指定查询

```sql
-- 查询所有字段
SELECT * FROM `t_sys_user`

-- 查询指定字段
SELECT `username` FROM `t_sys_user`

-- 使用别名
SELECT `username` AS 用户名 FROM `t_sys_user` AS u

-- 使用函数 concat(a,b) 拼接两者字符串
SELECT CONCAT('用户名: ',`username`) AS 新用户名 FROM `t_sys_user` AS u

-- 去重 distinct  去重重复数据 只显示一条
SELECT DISTINCT `username` AS 用户名 FROM t_sys_user
```

#### 4.2.2 表达式

数据库中的表达式: 文本值，列，NULL，函数，计算表达式，系统变量…

```sql
SELECT VERSION() --查询系统版本(函数)
SELECT 100*2-123 AS 结果 --用于计算(计算表达式)
SELECT @@auto_increment_increment --查询自增的步长 变量
SELECT now() --查询当前时间
```

#### 4.2.3 where 条件子句
基本运算符

| 运算符  | 语法           | 描述 |
| ------- | -------------- | ---- |
| and &&  | a and b a && b | 与   |
| or \|\| | a orb a \|\| b | 或   |
| Not !   | not a ! a      | 非   |

模糊查询

| 运算符      | 语法            | 描述                        |
| ----------- | --------------- | --------------------------- |
| BETWEEN     | between … and … | 在两者之间                  |
| IS NULL     | a is null       | 如果为 null,结果为真        |
| IS NOT NULL | a is not null   | 如果不为 null,结果为真      |
| **Like**    | **a like b**    | **a 匹配到 b,结果为真**     |
| In          | a in (a1,a2,a3) | 匹配 a 在 a1,a2,a3 其中之一 |

其中 like 还搭配了 %(0 到任意个字符) \_(一个字符) 使用

#### 4.2.4 联表查询(重点)

一共有 7 中 JOIN 查询

![img](https://img.kuizuo.cn/20201009150524563.png)

实际上用的最多的也就是以下三种，区别如下

| 操作       | 描述                                   |
| ---------- | -------------------------------------- |
| inner join | 如果表中至少有一个匹配，就返回该行     |
| left join  | 会返回左表中所有数据，即使右表没有匹配 |
| right join | 会返回右表中所有数据，即使左表没有匹配 |

```sql

-- 查询用户所属角色
select id, menu_name, component, menu_level, parent_id, menu_icon, path, sort, status
from t_sys_menu
where status = '0'
  and id in (select distinct ra.authority_id
             from t_sys_role_authority ra
                      inner join t_sys_user_role ur on ur.role_id = ra.role_id
                      inner join t_sys_role r on r.id = ur.role_id
             where ur.user_id = 'userid'
               and r.status = '0'
               and ra.type = '0')
order by sort;
```

#### 4.2.5 分页和排序

关键字 `limit` 和 `order by`，注：limit 最后使用

排序语法： ORDER BY 字段 排序类型

升序 ASC 降序 DESC

分页语法：LIMIT 起始值,页面大小

假设当前页面需展示 10 条数据（变量 pageSize），那么

第一页数据 LiMIT 0,10 (1-1)\*10

第二页数据 LiMIT 10,10 (2-1)\*10

第三页数据 LiMIT 20,10 (3-1)\*10

**第 N 页数据 LIMIT (N-1)\*pageSize,pageSize**

基于这样的原理，即可实现分页，大致过程如下

首先，接收到前端发送的分页请求，page 与 pageSize，那么与之对应的数据库查询语句为

```
SELECT * FROM user LIMIT (page-1)*pageSize,pageSize
```

总页数 = 数据总数/页面大小

#### 4.2.6 子查询

在 where 中，条件为固定的，想根据查询当前表的结果赋值到 where 条件中，则为子查询，注：子查询多数下查询速度较慢

**本质：在 where 语句中嵌套子查询语句**

子查询用的少，联表查询用的多。

```sql
SELECT * FROM t_sys_user
WHERE organization_id
IN (SELECT id FROM t_sys_organization);
```

#### 4.2.7 分组查询

关键字 group by

**注：group by 所要分组的字段，必须要在 select 中所选，且常搭配聚合函数所使用**

```sql
select organization_id ,count(*) as 数量 from t_sys_user group by organization_id
```

## 五、MySQL 函数

[官网地址](https://dev.mysql.com/doc/refman/8.0/en/functions.html)

### 5.1 常用函数

数学运算

```sql
SELECT RAND() --返回0~1之间的随机数
```

字符串

```sql
SELECT CHAR_LENGTH('这是一串文本') --返回字符串长度
SELECT CONCAT('JavaScript','是世界上最好用的语言') --拼接字符串
SELECT LOWER('Kuizuo') --到小写
SELECT UPPER('Kuizuo') --到大写
```

时间日期

```sql
SELECT CURRENT_DATE() --获取当前日期
SELECT CURDATE() --获取当前时间 与上面等价
SELECT NOW() --获取当前时间
SELECT LOCALTIME() --本地时间
SELECT SYSDATE() --系统时间
```

系统

```
SELECT SYSTEM_USER() -- 获取当前用户
SELECT USER() -- 获取当前用户  root@localhost
SELECT VERSION() --获取当前版本  8.0.21
```

### 5.2 聚合函数(用的多)

| 函数名    | 描述     |
| --------- | -------- |
| **COUNT** | **计数** |
| SUM       | 求和     |
| AVG       | 平均值   |
| MAX       | 最大值   |
| MIN       | 最小值   |
| …         | …        |

COUNT(列) —指定列，当值为 Null 不计数

COUNT(\*) —获取全部计数结果，不会忽略 NULL 值

COUNT(1) —忽略所有列，用 1 代表代码行，不会忽略 NULL 值

执行效率上：
列名为主键，count(列名)会比 count(1)快
列名不为主键，count(1)会比 count(列名)快
如果表多个列并且没有主键，则 count（1） 的执行效率优于 count(\*)
如果有主键，则 select count（主键）的执行效率是最优的
如果表只有一个字段，则 select count(\*)最优。

> 参考链接 [count(1)、count(\*)与 count(列名)的执行区别](https://www.cnblogs.com/Memories-off/p/10435558.html)

使用聚合函数，常常与分组 GROUP BY 和 HAVING 结合使用。


## 六、索引

索引（Index）是帮助 MySQL 高效获取数据的数据结构。

提取句子主干，就可也得到索引的本质：索引是数据结构

### 6.1 索引分类

一个表中，主键索引只能有一个，唯一索引可以有多个

- 主键索引（PRIMARY KEY）

  - 唯一的标识，主键不可重复，只能有一个列作为主键

- 唯一索引（UNIQUE KEY）

  - 避免重复的列出现，唯一索引可以重复，多个列，都可以标识为 唯一索引

- 常规索引（KEY/INDEX）

  - 默认的，index，key 关键字来设置

- 全文索引 （FULLTEXT）
  - 在特定的数据库引擎下才有

### 6.2 索引的使用

```
-- 显示所有的索引信息
SHOW INDEX FROM 表名

-- 添加一个全文索引 索引名 字段名
ALTER TABLE 表名 ADD FULLTEXT INDEX 索引名(字段名)

-- EXPLAIN 分析sql执行的状况
EXPLAIN SELECT * FROM student; -- 非全文索引

```


## 七、角色权限管理

```sql
-- 创建用户 CREATE USER 用户名 IDENTIFIED BY ‘密码’
CREATE USER test IDENTIFIED BY '123456'

-- 修改密码 (修改当前用户)
SET PASSWORD = PASSWORD('a123456')

-- 修改密码 (修改指定用户)
SET PASSWORD FOR test= PASSWORD('a123456')

-- 重命名
RENAME USER test TO zeyu

-- 用户授权
-- CRANT ALL PRIVILEGES ON 库.表 TO 用户
-- ALL PRIVILEGES 除了给别人授权(GRANT),其他权限都有
CRANT ALL PRIVILEGES ON *.* TO test

-- 查看权限
SHOW GRANTS FOR test
SHOW GRANTS FOR root@localhost
-- root 的权限 GRANT PROXY ON ''@'' TO 'root'@'localhost' WITH GRANT OPTION

-- 撤销权限 REVOKE 权限 ON 库.表 FROM 用户
REVOKE ALL PRIVILEGES ON *.* FROM test

-- 删除用户
DROP USER test
```


## 八、三大范式

**第一范式（1NF）：要求数据库表的每一列都是不可分割的原子数据项。**

不然获取数据时，不好处理

**第二范式（2NF）：在 1NF 的基础上，非码属性必须完全依赖于候选码（在 1NF 基础上消除非主属性对主码的部分函数依赖）**

第二范式需要确保数据库表中的每一列都和主键相关，而不能只与主键的某一部分相关（主要针对联合主键而言）。

每张表只描述一件事情

**第三范式（3NF）：在 2NF 基础上，任何非主属性不依赖于其它非主属性（在 2NF 基础上消除传递依赖）**

第三范式需要确保数据表中的每一列数据都和主键直接相关（属性依赖主键），而不能间接相关。

[关系型数据库设计：三大范式的通俗理解](https://www.cnblogs.com/wsg25/p/9615100.html)

**规范与性能问题**

关联查询的表不得超过三张表

- 在考虑规范成本与用户体验上，数据库的性能更加重要
- 故意给某些表添加一下冗余的字段，是多表查询变为单表查询。


