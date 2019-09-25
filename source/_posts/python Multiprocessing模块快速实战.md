---
title: python Multiprocessing模块快速实战
date: 2019-09-25 10:36:43
categories: "python"
tags: 
  - 技术
  - python
toc: true
---

## 背景

&emsp;&emsp;测试项目中，碰到一个场景，大体是需要验证多个表中的数据正确性。8000W的用户量，所以写一个脚本来抽查其中一部分。  
&emsp;&emsp;初步使用python写单线程脚本，跑1W数据，大约需要十几分钟。可见效率之低下。于是考虑使用Multiprocessing来加快执行速度，以及对比下效果。  
&emsp;&emsp;本文不做模块科普，只从实战角度讲起，教你如何快速把单线程变成多进程方式来加快执行速度。  

## Multiprocessing 模块使用
### 多进程使用要点概览：
- 创建Pool，进程池
- map,开始执行
- close， 关闭进程池
- join， 等待子进程执行完毕


## 代码demo
### 单线程版
```python
import pymysql
import time
# 数据库配置
db_config = {
  "host": "172.21.0.9",
  "port": 3306,
  "user": "root",
  "passwd": "ni cai bu dao",
  "db": "database_name"
}

# 数据库查询封装
class DB(object):
    def __init__(self):
        db = pymysql.Connect(**db_config)
        self.cursor = db.cursor()
        
    # 查询用户最近一笔流水时间
    def query_user_flow(self, user_id):
        ext = int(user_id) % 100
        sql = 'select ctime from database_name.user_flow_{} where user_id={} order by id desc limit 1;'.format(ext, user_id)
        self.cursor.execute(sql)
        res = self.cursor.fetchone()
        return res


if __name__ == '__main__':
    start_time = time.time()
    # 读取用户id
    f = open('./user_credit_bk.txt', 'r').readlines()
    # 写入流水时间到新文件
    nf = open('./user_time.txt', 'w')
    flow = DB()
    # 初始化一个字典
    user_dict = {}
    # 遍历userid
    for line in f:
        user = line.split('\t')[0]
        atime = flow.query_user_flow(user)
        # 将userid和最近流水时间设为字典
        user_dict.setdefault(user, atime[0])
    # 按照流水时间倒序排序
    new_list = sorted(user_dict.items(), key=lambda item: item[1], reverse=True)
    # 遍历列表，写入新文件中
    for item in new_list:
        nf.write('{time}\t{user}\n'.format(time=item[1], user=item[0]))
    nf.close()
    end_time = time.time()
    print('程序总共执行了{}s'.format(int(end_time - start_time)))

```
执行结果：
```
程序总共执行了144s
```

### 多进程版
```python
import pymysql
import time
from multiprocessing import Manager, Pool
# 数据库配置
db_config = {
  "host": "172.21.0.9",
  "port": 3306,
  "user": "root",
  "passwd": "ni cai bu dao",
  "db": "database_name"
}


# 数据库查询封装
class DB(object):
    def __init__(self):
        db = pymysql.Connect(**db_config)
        self.cursor = db.cursor()
        
    # 查询用户最近一笔流水时间
    def query_user_flow(self, user_id):
        ext = int(user_id) % 100
        sql = 'select ctime from database_name.user_flow_{} where user_id={} order by id desc limit 1;'.format(ext, user_id)
        self.cursor.execute(sql)
        res = self.cursor.fetchone()
        return res
        
        
def user_time(user_info):
    flow = DB()
    user = user_info.split('\t')[0]
    atime = flow.query_user_flow(user)
    user_dict.setdefault(user, atime[0])


if __name__ == '__main__':
    # 开始时间
    start_time = time.time()
    # 读取用户数据
    user_infos = open('./user_credit_bk.txt', 'r').readlines()
    # 写入流水时间到新文件
    nf = open('./user_time.txt', 'w')
    # 初始化一个字典(进程共享变量)
    user_dict = Manager().dict()
    # 创建10个进程
    with Pool(10) as pool:
        pool.map(user_time, user_infos)
        pool.close()
        pool.join()
    # 按照流水时间倒序排序
    new_list = sorted(user_dict.items(), key=lambda item: item[1], reverse=True)
    # 遍历列表，写入新文件中
    for item in new_list:
        nf.write('{time}\t{user}\n'.format(time=item[1], user=item[0]))
    nf.close()
    end_time = time.time()
    print('程序总共执行了{}s'.format(int(end_time-start_time)))
```
执行结果：
```
程序总共执行了30s
```
## 总结
&emsp;&emsp;可见多进程比单进程的快了将近5倍。  
&emsp;&emsp;其实Multiprocessing里还有非常多的用法，比如消息队列，进程锁等等。有兴趣的同学可以一起来讨论。