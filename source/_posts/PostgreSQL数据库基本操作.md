---
title: PostgreSQL数据库基本操作
date: 2018-04-12 11:41:25
tags:
  - PostgreSQL
   - 数据库
---

[postgresql学习站点](https://www.tutorialspoint.com/postgresql/index.htm)

1. 创建用户

```
sudo -s -u postgres
psql
postgres# CREATE USER xxxx1 WITH PASSWORD 'xxxx';
postgres# CREATE DATABASE xxxx2;
postgres# GRANT ALL PRIVILEGES ON DATABASE xxxx2 to xxxx1;
```

2. 修改密码
```
 alter user postgres with password 'foobar';
```

3. 创建数据库
 
```
createdb--encoding=UTF8 --owner=foo --template=template_postgis -Ufoo
 参数： --encoding=UTF8 设置数据库的字符集
         --owner=foo 设置数据库的所有者
         --tmplate=template_postgis 设置建库的模板，该模板支持空间数据操作
         --Ufoo 用foo用户身份建立数据库
```

