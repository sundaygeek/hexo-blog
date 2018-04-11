---
title: linux命令-压缩分割
date: 2018-04-10 17:50:34
tags:
  - linux命令
categories:
  - linux
---

# 1、tar压缩

tar -zcvf cm-11.tar.gz cm-11
//将cm-11文件夹压缩成cm-11.tar.gz

tar -jcvf cm-11.tar.gz cm-11
//将cm-11文件夹压缩成cm-11.tar.bz2

# 2、分割
压缩后的文件太大，需要将cm-11.tar.gz分割成N个指定大小的文件
split -b 4000M -d -a 1 cm-11.tar.gz cm-11.tar.gz.

//使用split命令，-b 4000M 表示设置每个分割包的大小，单位还是可以k
// -d "参数指定生成的分割包后缀为数字的形式
//-a x来设定序列的长度(默认值是2)，这里设定序列的长度为1

# 3、解压

tar -zxvf cm-11.tar.gz
tar -jxvf cm-11.tar.bz2


# 4、分割后的压缩包解压命令如下

cat cm-11.tar.gz.* | tar -zxv

