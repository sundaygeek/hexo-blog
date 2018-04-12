---
title: python-mysql-python2和3的差异
date: 2018-04-12 11:04:32
tags:
  - mysql
  - python
  - sqlalchemy
  - MySQLdb
---

运行
```
from sqlalchemy import create_engine
engine = create_engine('mysql+mysqldb://scott:tiger@localhost/foo')
```
提示：
No module named MySQLdb

对于python2和python3安装的软件包不一样
```
pip install mysql-python (mix os/ python 2)
pip install mysqlclient (mix os/ python 3)

apt-get install python-mysqldb (Linux Ubuntu, ...)
```
