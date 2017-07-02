---
layout: post
title: MySQL常用命令——表操作
date: 2017-05-19
categories: blog
tags: [系统分析与设计, 数据库]
description: MySQL常用命令——表操作
---

## 1. 建表

命令：create table <表名> ( <字段名1> <类型1> [,..<字段名n> <类型n>]);

```sql
mysql> create table MyClass(
> id int(4) not null primary key auto_increment,
> name char(20) not null,
> gender int(4) not null default '0',
> degree double(16,2));
```

## 2. 获取表结构
命令： desc 表名，或者show columns from 表名

```sql
mysql>DESCRIBE MyClass  
mysql> desc MyClass;   
mysql> show columns from MyClass;
```

## 3. 删除表

命令：drop table <表名>
例如：删除表名为 MyClass 的表

```sql
mysql> drop table MyClass;
```

## 4. 插入数据
命令：insert into <表名> [( <字段名1>[,..<字段名n > ])] values ( 值1 )[, ( 值n )]
例如，往表 MyClass中插入二条记录, 这二条记录表示：编号为1的名为Tom的成绩为96.45, 编号为2 的名为Joan 的成绩为82.99，编号为3 的名为Wang 的成绩为96.5.

```sql
mysql> insert into MyClass values(1,'Tom',96.45),(2,'Joan',82.99), (2,'Wang', 96.59);
```

## 5. 查询表中的数据

#### 1) 查询所有行

命令： select <字段1，字段2，...> from < 表名 > where < 表达式 >
例如：查看表 MyClass 中所有数据

```sql
mysql> select * from MyClass;
```

#### 2) 查询前几行数据

例如：查看表 MyClass 中前2行数据

```sql
mysql> select * from MyClass order by id limit 0,2;
```

或者：

```sql
mysql> select * from MyClass limit 0,2;
```

## 6. 删除表中数据

命令：delete from 表名 where 表达式
例如：删除表 MyClass中编号为1 的记录

```sql
mysql> delete from MyClass where id=1;
```

## 7. 修改表中数据

update 表名 set 字段=新值,… where 条件

```sql
mysql> update MyClass set name='Mary' where id=1;
```

## 8. 在表中增加字段

命令：alter table 表名 add字段 类型 其他;
例如：在表MyClass中添加了一个字段passtest，类型为int(4)，默认值为0

```sql
mysql> alter table MyClass add passtest int(4) default '0'
```

## 9. 更改表名

命令：rename table 原表名 to 新表名;
例如：在表MyClass名字更改为YouClass

```sql
mysql> rename table MyClass to YouClass;
```

## 10. 更新字段内容
update 表名 set 字段名 = 新内容  
update 表名 set 字段名 = replace(字段名,'旧内容','新内容');   
文章前面加入4个空格

```sql
update article set content=concat('　　',content);
```