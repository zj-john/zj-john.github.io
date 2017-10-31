---
title: Django:No module named MYSQLdb
categories:
  - tips
tags:
  - python
  - django
  - MYSQLdb
date: 2017-09-14 11:20:24
toc: true
---
> 问题：使用virtualenv搭建Django老项目，遇到Django:No module named MYSQLdb

<!-- more -->

# centos   
You need to use one of the following commands. Which one depends on what OS and software you have and use.

 1. **easy_install mysql-python**(mix os)
 2. **pip install mysql-python** (mix os)
 3. **apt-get install python-mysqldb**(Linux Ubuntu, ...)
 4. **cd /usr/ports/databases/py-MySQLdb && make install clean**(FreeBSD)
 5. **yum install MySQL-python** (Linux Fedora, CentOS ...)【亲测可用】

For Windows, see this answer: https://stackoverflow.com/q/21440230/4646678

# Windows

如果你全局可以安装mysql-python，把python目录 (默认C:/python27)下lib/site-package目录中的以下4个文件复制到你的虚拟环境的site-packages目录下，就可以了。

- MySQL_python-1.2.4-py2.7.egg-info(folder)
- MySQLdb(folder)
- _mysql_exceptions.py/.pyc/.pyo
- _mysql.pyd  
