---
title: PyMysql基础
date: 2024-03-11 20:10:49
excerpt: Python中用于操作MYSQL数据库的第三方库
tags:
  - 数据库
categories:
  - [Python,数据库]
---

# 1. PyMysql介绍

> Python中用于操作MYSQL数据库的第三方库，其操作流程大致如下👇

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202401222207392.png" alt="image-20240122220657270" style="zoom:67%;" />

```python
# 得到的conn是连接数据库的对象
# 如果是增删改操作，则conn还需要执行commit()/rollback()才能发挥作用
conn = pymysql.connect(host,port,user,password,database,charset)
```

+ host：数据库所在主机（IP地址）
+ port：数据库所使用端口号
+ user：连接数据库使用的用户名
+ password：连接数据库使用的密码
+ database：准备连接的数据库名
+ charset：字符集，常用 utf8

```python
# 可以将conn理解为数据库与代码之间的通道，cursor理解为通道间来回运输的货车
# 游标初始指向0行，执行时先指向下一行，再获取当前行的结果
cursor = conn.cursor()
```

+ fetchmany(size)：获取当前游标往下size条结果
+ fetchall()：获取当前游标往下的所有结果
+ fetchone()：获取当前游标往下的第一条结果
+ 通过 cursor.rownumber = 0 可以使得下标回零
+ cursor.rowcount 查看受影响结果行数

**例子**

```python
import pymysql
# 建立连接
conn = pymysql.connect(host='localhost',port=3306,
                user='root',password='admin',
                database='reggie',charset='utf8')
# 获取连接对应的游标
cursor = conn.cursor()
# 利用游标执行sql语句
cursor.execute('select * from dish')

# fetchmany(size)：获取当前游标往下size条结果
# fetchall()：获取当前游标往下的所有结果
# 游标初始指向0行，执行时先指向下一行，再获取当前行的结果
# 通过 cursor.rownumber = 0 可以使得下标回0；cursor.rowcount查看结果行数
res = cursor.fetchone()
print('res = ',res)
# 关闭游标
cursor.close()
# 关闭连接
conn.close()
```

# 2. PyMysql工具类封装

```python
import pymysql

class DBUtils:
    # 类属性
    conn = None
    # 查询一条数据
    @classmethod
    def select_one(cls,sql):
        cursor= None
        res = None
        try:
            # 获取连接，存放再cls.conn
            cls.__get_conn()
            cursor = cls.conn.cursor()
            cursor.execute(sql)
            res = cursor.fetchone()
        except Exception as err:
            print('查询sql错误：',str(err))
        finally:
            cursor.close()
            cls.__close_conn()
            return res

    # 增删改
    @classmethod
    def uid_sql(cls, sql):
        cursor = None
        try:
            cls.__get_conn()
            cursor = cls.conn.cursor()
            cursor.execute(sql)
            # 输出执行sql语句后影响的行数
            print(cls.conn.affected_rows())
            cls.conn.commit()
        except Exception as err:
            cls.conn.rollback()
            print('增删改sql错误：',err)
        finally:
            cursor.close()
            cls.conn.close()

    @classmethod
    def __get_conn(cls):
        if cls.conn is None:
            cls.conn = pymysql.connect(host='localhost', port=3306,
                                       user='root', password='admin',
                                       database='ssm_database', charset='utf8')

    @classmethod
    def __close_conn(cls):
        if cls.conn is not None:
            cls.conn.close()
            cls.conn = None
            
if __name__ == '__main__':
    # 往ssm_database数据库中的stu表添加一行数据
    DBUtils.uid_sql('insert into stu (id,name,score,score2) values (1,"小帅",99,98)')
    # 查询ssm_database数据库中的stu表的内容
    res = DBUtils.select_one('select * from stu')
    # 输出查询结果
    print(res)
```
