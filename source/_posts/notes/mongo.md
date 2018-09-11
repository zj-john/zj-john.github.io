---
title: mongo 基础
categories:
  - notes
tags:
  - MongoDB
toc: true
date: 2018-02-26 15:26:20
---

MongoDB学习记录

<!-- more -->
## 术语
| SQL术语/概念        | MongoDB术语/概念           | 解释/说明  |
| ------------- | ------------- | ----- |
|database	|database	|数据库|
|table	|collection	|数据库表/集合|
|row	|document	|数据记录行/文档|
|column	|field	|数据字段/域|
|index	|index	|索引|
|table |joins	 	|表连接,MongoDB不支持|
|primary key	|primary key	|主键,MongoDB自动将_id字段设置为主键|

## 特殊库
有一些数据库名是保留的，可以直接访问这些有特殊作用的数据库。
* admin：  
  从权限的角度来看，这是"root"数据库。要是将一个用户添加到这个数据库，这个用户自动继承所有数据库的权限。一些特定的服务器端命令也只能从这个数据库运行，比如列出所有的数据库或者关闭服务器。
* local:  
这个数据永远不会被复制，可以用来存储限于本地单台服务器的任意集合
* config:   
当Mongo用于分片设置时，config数据库在内部使用，用于保存分片的相关信息。

>MongoDB区分类型和大小写

## 常用命令
* show dbs    
显示所有数据的列表

* db    
可以显示当前数据库对象或集合

* use DB_NAME  
 连接到一个指定的数据库 如果数据库不存在，则创建数据库，否则切换到指定数据库。

* db.DB_NAME.insert({"name":"菜鸟教程"})

* db.dropDatabase()  删除当前数据库

* show tables  
展示集合

* db.COLLECTION_NAME.drop()  
删除集合

* db.COLLECTION_NAME.insert(document)

* db.COLLECTION_NAME.find()

* db.COLLECTION_NAME.save(document)  
如果不指定_id 字段 save() 方法类似于 insert() 方法,如果指定_id 字段，则会更新该_id 的数据。

* db.COLLECTION_NAME.update({'title':'MongoDB 教程'},{$set:{'title':'MongoDB'}},{multi:true})   
更新、muti:true 更新多条

* db.col.remove({'title':'MongoDB 教程'})

* db.COLLECTION_NAME.ensureIndex({KEY:1})   
创建索引：语法中 Key 值为你要创建的索引字段，1为指定按升序创建索引，如果你想按降序来创建索引指定为-1即可

* mongo mongo_address:55944/db_name -u "username" -p "password"  
连接数据库
