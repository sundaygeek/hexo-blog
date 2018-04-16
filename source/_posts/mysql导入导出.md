---
title: mysql导入导出
date: 2018-04-16 14:09:15
tags:
 - 数据库
 - mysql
categories:
 - 数据库
 - mysql
---

导入导出
# 1.导入

用文本形式插入数据

LOAD DATA LOCAL INFILE 'd:/mysql.txt' INTO TABLE mytable;
导入.sql

use database;
source d:/mysql.sql
从另外一张表往这张表插入

INSERT INTO tab1(f1,f2)
SELECT a.f1, a.f2
FROM a WHERE a.f1='a'


# 2.备份

导出要用到MySQL的mysqldump工具，基本用法是：

mysqldump [OPTIONS] database [tables]
备份MySQL数据库的命令

mysqldump -hhostname -uusername -ppassword databasename  backupfile.sql
备份MySQL数据库为带删除表的格式，能够让该备份覆盖已有数据库而不需要手动删除原有数据库。

mysqldump -–add-drop-table -uusername -ppassword databasename  backupfile.sql
直接将MySQL数据库压缩备份

mysqldump -hhostname -uusername -ppassword databasename | gzip  backupfile.sql.gz
备份MySQL数据库某个(些)表

mysqldump -hhostname -uusername -ppassword databasename specific_table1 specific_table2  backupfile.sql
同时备份多个MySQL数据库

mysqldump -hhostname -uusername -ppassword –databases databasename1 databasename2 databasename3  multibackupfile.sql
仅仅备份数据库结构

mysqldump –no-data –databases databasename1 databasename2 databasename3  structurebackupfile.sql
备份服务器上所有数据库

mysqldump –all-databases  allbackupfile.sql
还原

# 3.还原MySQL数据库的命令

mysql -hhostname -uusername -ppassword databasename < backupfile.sql
mysql -hhostname -ppassword databasename tablename < backuptablefile.sql
还原压缩的MySQL数据库

gunzip < backupfile.sql.gz | mysql -uusername -ppassword databasename
将数据库转移到新服务器

mysqldump -uusername -ppassword databasename | mysql –host=*.*.*.* -C databasename
将查询结果导入外部文件

SELECT a,b,a+b 
FROM test_table
INTO OUTFILE '/tmp/result.txt'
FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '"'
LINES TERMINATED BY '\n'

或者

mysql -u you -p -e "SELECT ..."   file_name

