---
title: mysql学习笔记
date: 2016-06-30 11:21:38
categories: mysql
tags:
---
```bash
1. 数据库操作
SHOW DATABASES; 查看数据库列表
CREATE DATABASE database_name; 创建数据库
DROP DATABASE database_name; 删除数据库
SHOW CREATE DATABASE database_name; 查看数据库信息
SHOW ENGINES \G 查看数据库支持的存储引擎
SHOW VARIABLES LIKE 'storage_engine'; 查看默认存储引擎
2. 数据表操作
创建数据表
CREATE TABLE tb_1(
id int(11) PRIMARY KEY,
name varchar(22)
);
SHOW TABLES; 查看数据表列表
PRIMARY KEY 使用主键约束
NOT NULL 非空性约束
UNIQUE 唯一性约束
DEFAULT 11 默认约束
AUTO_INCREMENT 表属性值自动增加
DESCRIBE tb_1; DESC tb_1;(简写) 查看数据表结构
SHOW CREATE TABLE tb_1; 查看表的详细结构
ALTER TABLE tb_1 RENAME tb_2; 修改表名
ALTER TABLE tb_1 MODIFY name VARCHAR(50); 修改字段数据类型
ALTER TABLE tb_1 CHANGE salary sala VARCHAR(10); 修改字段名
ALTER TABLE tb_1 ADD manaId int(10); 添加无完整性约束条件字段
ALTER TABLE tb_1 ADD manaId int(10) NOT NULL; 添加有完整性约束条件字段
ALTER TABLE tb_1 ADD manaId int(10) FIRST; 在表的第一列添加一个字段
ALTER TABLE tb_1 ADD manaId int(10) AFTER name; 在表的指定列添加一个字段
ALTER TABLE tb_1 DROP manaId; 删除字段
ALTER TABLE tb_1 MODIFY manaId VARCHAR(10) FIRST; 修改字段为第一个字段
ALTER TABLE tb_1 MODIFY manaId VARCHAR(10) AFTER name; 修改字段到指定列之后
ALTER TABLE tb_3 ENGINE=InnoDB; 更改表的存储引擎
DROP TABLE IF EXISTS tb_1; 删除数据表
CHECK TABLE tb_1; 检查表是否有错误
OPTIMIZE TABLE tb_1; 整理存储空间碎片,整理空间
insert into su_tb (id, name) select id, name from su_tb; 插入查询的数据表
```
