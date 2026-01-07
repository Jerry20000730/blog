---
title: MySQL Cheatsheet
tags: [MySQL]
categories: MySQL
cover: images/MySQL/cover.svg
excerpt: MySQL is a database management system and it has been one of the most popular open source SQL database management system. The system is developed, distributed, and supported by Oracle Corporation. MySQL databases are relational. A relational database stores data in separate tables rather than putting all the data in one big storeroom. 
---
<!-- toc -->

## Classification
1. Data Definition Language (DDL)
```sql
CREATE
```
```sql
DROP
```
```sql
ALTER
```

2. Data Manipulation Language (DML)
```sql
INSERT
```
```sql
DELETE
```
```sql
UPDATE
```

3. Data Query Language (DQL)
```sql
SELECT
```
```sql
WHERE
```

4. Data Control Language (DCL)
```sql
GRANT
```
```sql
REVOKE
```

5. Transaction Control Language (TCL)
```sql
COMMIT
```
```sql
ROLLBACK
```
## MySQL data types

### Numerical type
| Integer | INTEGER/INT, SMALLINT, TINYINT, MEDIUMINT, BIGINT |
| --- | --- |
| Fixed-point | DECIMAL, NUMERIC |
| Floating-point | FLOAT, DOUBLE |

### Date/time tyoe
DATE, DATETIME, TIMESTAMP, TIME, YEAR

### String type
CHAR, VARCHAR, BINARY, VARBINARY, BLOB, TEXT, ENUM, SET

## MySQL DDL & DML

1. Create table
```sql
CREATE table player (
	id INT,
  name VARCHAR(100),
  level INT,
  exp INT,
  gold DECIMAL(10, 2)
);
```

2. Insert (single) Data
```sql
INSERT INTO player (id, name, level, exp, gold) VALUES (1, '张三', 1, 1, 1);
```

3. read data
```sql
SELECT * from player;
```

4. Insert (multiple) data
```sql
INSERT INTO player (id, name) VALUES (2, '李四'), (3, '王五')
```
（blank means NULL, because we did not specify the default data）

5. Modify the structure of table
```sql
-- 将level的默认值设置为1
ALTER TABLE player MODIFY LEVEL INT DEFAULT 1;
-- 将name的类型改为VARCHAR(200)
ALTER TABLE player MODIFY COLUMN name VARCHAR(200);
```

6. Update the data in the table
```sql
-- 只修改李四的数据
UPDATE player set level = 1 where name = '李四';
-- 全部修改（比较危险）
UPDATE player set level = 1;
-- 修改多列值
UPDATE player set exp=0, gold=0;
```

7. create index
```sql
-- unique: 唯一索引
-- full text: 全文索引
-- spacial: 空间索引
CREATE [UNIQUE|FULL TEXT|SPATIAL] INDEX index_name 
ON table_name (index_col_name, ...)
```

8. delete index
```sql
drop index index_name on table_name
```

9. alter table by inserting index
```sql
alter table table_name add index name_index (col_name)
```

## MySQL DQL

### SELECT
```sql
SELECT name, level
FROM player
WHERE level = 1
ORDER BY gold
LIMIT 3;
```

### WHERE
```sql
SELECT * FROM player where level = 1;
SELECT * FROM player where level > 1;
SELECT * FROM player where level > 1 and level < 5;
-- Priority: NOT > AND > OR
SELECT * FROM player where level > 1 AND (level < 5 OR exp > 1) AND exp < 5;
```

### IN
```sql
SELECT * FROM player WHERE level IN (1, 3, 5);
```

### BETWEEN...AND...
```sql
SELECT * FROM player WHERE level BETWEEN 1 AND 10;
```

### NOT
```sql
SELECT * FROM player WHERE level NOT BETWEEN 1 AND 10;
```

### LIKE
| **char** | **match** |
| --- | --- |
| `%` | any number of any character |
| `_` | any one character |

```sql
SELECT * FROM player WHERE name LIKE '王%';
```

### Regular Expression
| **char** | **match** |
| --- | --- |
| `.` | any character |
| `^` | begin |
| `$` | end |
| `[abc]` | any character inside bracket |
| `[a-z]` | any character in range(a, z) |
| A &#124; B | A or B |

```sql
SELECT * FROM player WHERE name REGEXP '[0-9]';

-- find name start with 王 and only two character (second character
-- can be anything)
SELECT * FROM player WHERE name REGEXP '^王.$';
```

### NULL (这个比较特殊）
```sql
SELECT * FROM player WHERE email is NULL;
-- note the language should not be: SELECT * FROM player WHERE email = null
```

### NULL != ' ' (empty string)
```sql
SELECT * FROM player WHERE email = '' or email is NULL;
```

### Sort
```sql
-- sort by level in descending order
SELECT * FROM player ORDER BY level DESC;
-- sort by level in descending order, if order is same,
-- then exp in ascending order
SELECT * FROM player ORDER BY level DESC, exp ASC;
```

### EXISTS
to know whether a subquery exists. 0 means no, and 1 means yes.
```sql
SELECT EXISTS(SELECT * FROM player WHERE level > 10)
```

### Aggregate Functions

#### AVG/COUNT/SUM/MAX/MIN
```sql
SELECT AVG(level) FROM player
```

#### GROUPING
```sql
SELECT sex, count(*) FROM player GROUP BY sex;
SELECT level, count(level) FROM player GROUP BY level;
```

#### HAVING
```sql
SELECT level, count(level) from player GROUP BY level HAVING COUNT(level) > 4
```

#### LIMIT
```sql
SELECT SUBSTR(name, 1, 1), COUNT(SUBSTR(name, 1, 1)) from player 
GROUP BY SUBSTR(name, 1, 1)
HAVING COUNT(SUBSTR(name, 1, 1)) >= 5
ORDER BY SUBSTR(name, 1, 1) DESC
-- we want the first 3 elements
LIMIT 3
```

### DISTINCT (remove repeated element)
```sql
SELECT DISTINCT sex from player
```

### UNION (合并查询结果集)
```sql
SELECT * FROM player where level BETWEEN 1 AND 3
UNION
SELECT * FROM player WHERE exp BETWEEN 1 AND 3;

-- we want to keep record that satisfy both conditions
-- because by default UNION remove repeated element

SELECT * FROM player WHERE level BETWEEN 1 AND 3
UNION ALL
SELECT * FROM player WHERE exp BETWEEN 1 AND 3;
```

### INTERSECT (交集)
```sql
SELECT * FROM player WHERE level BETWEEN 1 AND 3
INTERSECT
SELECT * FROM player WHERE exp BETWEEN 1 AND 3
```

### EXCEPT (差集)
```sql
SELECT * FROM player WHERE level BETWEEN 1 AND 3
EXCEPT
SELECT * FROM player WHERE level BETWEEN 1 AND 3
```

## MySQL Subquery
sometimes we need one query to be the condition of another query. 
```sql
SELECT * FROM player WHERE level > (SELECT AVG(level) FROM player)

-- remember to use alias
SELECT 
	level, ROUND(SELECT AVG(level) FROM player) as average, 
  level - ROUND(SELECT AVG(level) from player) as diff
FROM player

-- can also used in creating new table
create table new_player select * from player where level < 5;

-- insert new data
insert into new_player select * from player where level between 6 and 10;
```

## MySQL Table Association

### INNER JOIN
```sql
-- display what both table have
-- by linking the foreign key
select * from player
inner join equip
on player.id = equip.player.id
```

### LEFT JOIN
```sql
-- display what left table have
-- by linking the foreign key
-- if right table did not have the info
-- will fill NULL
-- (under this scenario, left table will display fully)

select * from player
left join equip
on player.id = equip.player.id
```

### RIGHT JOIN
```sql
-- similarly, display what right table have
-- by linking the foreign key
-- if left table did not have the info
-- will fill NULL
-- (under this scenario, right table will display fully)

select * from player
right join equip
on player.id = equip.player.id
```

## MySQL in terminal

1. Login (local)

Login the localhost mysql database, `-u`specifies the username, `-p`specify to use password to login
```shell
$ mysql -u root -p
```

2. Login (remote)

Login the remote mysql database, `-h`specifies the IP address of the host machine
```shell
$ mysql -h 10.211.55.5 -u root -p
```

3. Switch database
```shell
mysql> use database_name;
```

4. Show database
```shell
mysql> show databases;
```

5. Create a database
```shell
mysql> create database game;
```

6. Create tables
```shell
mysql> create table player (
	id INT,
	name VARCHAR(100),
	level INT,
	exp INT,
	gold DECIMAL(10, 2) <- 长度为10保留两位小数的十进制数值
)
```

7. Describe the structure of the table
```
mysql> DESC player;
```

8. CRUD
```shell
mysql> SELECT/UPDATE/DELETE/ALTER ...
```

9. exit
```shell
mysql> exit;
```

10. export database
```shell
$ mysqldump -u root -p game player > player.sql
```

11. import database
```sql
mysql> mysql -u root -p game < game.sql
```

# Reference
1. 【一小时MySQL教程 Geekhour】[https://www.bilibili.com/video/BV1AX4y147tA/?share_source=copy_web&vd_source=590654268d829d1b7b4303295980cd78](https://www.bilibili.com/video/BV1AX4y147tA/?share_source=copy_web&vd_source=590654268d829d1b7b4303295980cd78)