---
title: Mysql学习笔记（1）
tags:
  - 速查
  - 笔记
url: 41.html
id: 41
categories:
  - Linux
date: 2016-04-04 22:57:54
---


由于平时使用Mysql比较少，防止隔段时间忘记学过的内容，就在这里记录一下学习笔记，以备后用。 Mysql 5.5 on Linux  
数据库的链接：            mysql -h localhost -u root -p  
列出所有数据库：        show databases; 
创建数据库：                create database db\_name; 
切换到某个数据库：   use db\_name; 
创建表：                        create table tableb_name (  属性名 数据类型, 属性名 数据类型....  ); 
数据类型详细表： [传送门](http://www.runoob.com/mysql/mysql-data-types.html) 
修改表名：                    alter table old\_name rename new\_name 
列出数据库中的表：   show tables; 
删除数据库（包含表）：drop database db\_name; 
删除表：                        drop table table\_name; 
删除表内容：                delete from table\_name；   
字段操作： 
  增加字段到末尾:            alter table table\_name add 属性名 数据类型; 
  增加字段到首位置:        alter table table\_name add 属性名 数据类型 first;
  指定字段后增加字段:    alter table table\_name add 属性名 数据类型 after 属性名; 删除字段:                         alter table table\_name drop 属性名; 
  修改字段的数据类型:    alter table table\_name modify 属性名 新数据类型; 
  修改属性名，数据类型: alter table table\_name change 旧属性名 新(旧)属性名 新(旧)数据类型; 
  修改字段位置:                 alter table table\_name modify 属性名1 数据类型 first|after 属性名2;
