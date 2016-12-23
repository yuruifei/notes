---
title: python_mz 
tags: python,笔记
grammar_cjkRuby: true
---

# 1. 数据库操作
## 1.1 Python DB API 2.0
### 1.1.1 connect()
connect成功后悔返回连接对象，连接对象有如下方法
```sql
cursor()
commit()
rollback()
close()
```

### 1.1.2 cursor()

```sql
callproc(...)
execute(...)
executemany()
fetchone()
fetchmany()
fetchall()
nextset()
arraysize
setinputsize
setoutputsize

```

## 1.2 sqlite3
### 1.2.1 基础操作
```python
#连接数据库
sqlite3.connect('数据库文件路径名')
sqlite3.connect(':memory:')
#获取游标
con.cursor()
#用游标对象方法操作数据库
cur.execute()
cur.executescript()
cur.fetchall()
#提交事务
con.commit()
con.rollback()
#关闭连接
con.close()
```
### 1.2.2 行对象
```python
from sqlite3 import connect, Row

db_name = 'testdb'
con = connect(db_name)
con.row_factory = Row
cur = con.cursor()

cur.execute('select * from star')
row = cur.fetchone()

print(type(row))  #<class 'sqlite3.Row'>
x = row['name']  #以列名访问
x = row[1]   # 以索引号访问
con.close()

```

### 1.2.3 批量操作
#### 1.2.3.1 通过executemany
```python
from sqlite3 import connect, Row

db_name = 'testdb'
con = connect(db_name)
con.row_factory = Row
cur = con.cursor()

rows = [(1, 'lily', 12, 'beijing'),(1, 'bob', 13, 'shanghai')]
cur.executemany('insert into star (id, name, age, addr) values(?, ?, ?, ?,)', rows)
con.commit()
con.close()
```

#### 1.2.3.2 通过executescript
```python
from sqlite3 import connect

db_name = 'testdb'
con = connect(db_name)
cur = con.cursor()
sql_str = """
create table test(id integer, name text);
insert into test (id, name) valus(1, 'jim');
```
### 1.2.4 使用自定义函数
基本函数
```python
from sqlite3 import connect Row
import binascii

db_name = 'test.db'

def encypt(mydata):
	crc = str(binascii.crc32(mydata.encode()))
	while len(crc) < 10:
		crc = '0' + crc
	return mydata + crc
	
def check(mydata):
	if len(mydata) < 11:
		return None
	crc = str(binascii.crc32(mydata[:-10].encode()))
	while len(crc) < 10:
		crc = '0' + crc
	if crc == mydata[-10:]:
		return mydata[:-10]

class AbsSum:
	def __init__(self):
		self.s = 0
	def step(self, v):
		self.s += abs(v)
	def finalize(self):
		return self.s


con = connect(db_name)
con.create_function('check_q', 1, check)

cur = con.cursor()

sql_script = """
drop table if exists testa;
create table testa(id integer, name text);
insert into testa (id, name) values (3, "%s");
insert into testa (id, name) values (4, "%s");
"""
names = ['lily', 'green']
names = tuple(encrypt(i) for i in names)
sql_script = sqlscript % names

cur.executescript(sql_script)
cur.execute('select id, check_q(name) from testa')
for item in cur:
	print(item)
con.commit()
con.close()

```
聚合函数
```python
from sqlite3 import connect Row
import binascii

db_name = 'test.db'

class AbsSum:
	def __init__(self):
		self.s = 0
	def step(self, v):
		self.s += abs(v)
	def finalize(self):
		return self.s


con = connect(db_name)
con.create_function('abssum', 1, AbsSum)

cur = con.cursor()

sql_script = """
drop table if exists testa;
create table testa(id integer, name text, score integer);
insert into testa (id, name) values (3, "lily", 8);
insert into testa (id, name) values (4, "green", -7);
"""

cur.executescript(sql_script)
cur.execute('select id, abssum(score) from testa')  
for item in cur:     # 结果是(15,)
	print(item)   
	
con.commit()
con.close()

```

### 1.2.5 二进制文件操作

数据类型对应关系

|  python  |  database   |
| ---: | :--- |
|   int  |  integer   |
|   str  |  text   |
|   None | NULL    |
|   bytes  |   blob  |

```python
f = open('hha.jpg', 'rb')
cur.execute('insert into testa(id, data) values (3, ?)', (f.read()))
```

## 1.3 mariaDB
关于mariaDB [百度百科](http://baike.baidu.com/link?url=qi2T7BUv3Jsf8Iq0jdBsQyLtlV2aLPCu-AQe51iy9ablX7SjgEVmWYy6eWQhM-XxgXfOfJPTaCVAO6hzU7_L-_) 


### 1.3.1 数据库环境的建立
1.下载压缩包，并解压
2.配置my.ini (可将默认配置 my-large.ini 拷贝到my.ini)
3.启动mysqld
3.1 命令行启动
```cmd
bin\mysqld --consle
bin\mysqladmin -u root password
bin\mysqladmin -u root -p passward
```
3.2 注册为windows服务,服务会随windows启动而启动
```cmd
mysqld install mariadb
net start mariadb
```
### 1.3.2 操作mariadb数据库
需要用第三方库来连接mariaDB，目前有支持python3的有pymysql, MYSQL-Pytion, MYSQL官方链接库
MYSQLdb 只支持python2
```python
from mysql.connector import connect # MYSQL官方链接库

db_name = {
	"host" : "localhost",
	"port" : 3306,
	"user" : "root",
	"password" : "123456",
	"database" : "test",
}

# db_name = {"database" : ":memory:"} 兼容sqlite3

con = connect(**db_name) # **args为字典参数  *args 接受可变参数或列表参数
cur = con.cursor()

#与sqlite3不同，这里的占位符为%s
row = (5, "tom", "tianjin")
cur.execute("insert into test (id, name, place) values ( %s, %s, %s)", row)
con.commit()
con.close()
```
## 1.4 ORM
对象关系映射（Object Relation Mapping），是一种程序技术，用于实现面向对象编程语言里不同类型系统的数据之间的转换。从效果上说，它其实是创建了一个可在编程语言里使用的--“虚拟对象数据库”。
SQLAlchemy 是一个开源ORM工具，用pip安装：
```bash
pip install SQLAlchemy
```
SQLAlchemy使用
```python
from sqlalchemy import create_engine, String, Column
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

# 0. 构造数据库连接字符串
db_name = 'mysql+mysqlconnector://root:123456@localhost:3306/test'

# 1. 构造声明基类
Base = declarative_base()

# 2. 声明对象关系映射类
class User(Base):
	__tablename__ = 'user'
	id = Column(Integer, primary_key = True)
	name = Column(String(50))
	
# 3. 创建数据库的连接
engine = create_engine(db_name)

# 4. 创建表
base.metadata.create_all(engine) 

# 5.创建会话
Session = sessionmaker(bind=engine)
session = Session()

# 6 使用会话访问数据库
u = User()
u.id = 1
u.name = 'lily'
sesion.add(u)
session.commit()

u1 = session.query(User).filter_by(id=1).first() #查询

u1.name = 'bob'
session.commit()  # 修改

session.delete(u1)
session.commit()  #删除

u2 = session.query(User)filter(User.id == 3).first()

us = session.query(User).all()
uss = session.query(User).order_by('id').all()
```
关系的建立包括一对多，多对多
```sql
todo
```

## 1.5 MongoDB
概念
1.集合 由一系列文档组成相当于表
2.文档 有一系列key-value对组成相当于记录
3.支持嵌套

### 1.5.1 数据库环境
1.下载mongodb并解压
2.bin目录下mongod.exe启动数据库，mongo.exe是客户端
```cmd
bin\mongod -- nojournal --path .   //无日志，当前目录
bin\mongo

use db_name 

show dbs
show collections


db.blog.insert({'id':1, 'name':'yrf'})

db.blog.find()

db.blog.find({'id':1})

db.blog.findOne()

db.blog.update({'id':2}),'$set':{'name':'YRF'})  //如果name不存在，则新增一条kv对。  $set是修改器，另外还有$unset, $inc

db.blog.remove({'id':3})


```



## 1.6 ODM
