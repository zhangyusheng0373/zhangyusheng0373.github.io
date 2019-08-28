---
title: flask框架实战--使用yaml配置文件
date: 2019-08-28 21:54:43
categories: "python", "flask框架"
tags: 
  - 技术
  - python
toc: true
---

## 背景
众所周知，flask框架非常轻量且灵活。项目中可能会碰很多配置项(如数据库账号、不同环境的URL域名、一些邮箱等配置)，经考量后，使用PyYAML模块，将众多配置放到统一的yaml配置文件中，来加载众多配置，方便统一管理。

## 使用

### 何为YAML文件？
YAML是一种直观的能够被电脑识别的的数据序列化格式，容易被人类阅读，并且容易和脚本语言交互。YAML类似于XML，JSON等，但是语法简单得多，对于转化成数组或可以hash的数据时是很简单有效的。

### YAML文件格式
```
# yaml格式
# 使用#号注释
USER_CENTER_URL: "https://xx.example.com/ba037"
# 数据库配置
ORDER_DATABASE_CONFIG: {
    "host": "172.21.0.9",
    "port": 3306,
    "user": "root",
    "passwd": "password",
    "db": "database_name"
  }
USER:
 - ID: 1
   NAME: "a"
 - ID: 2
   NAME: "b"
```
在python中，使用PyYAML解析后，以字典(dict)格式返回对象。然后按照字典方式取值即可，输出如下：
```
{
   "USER_CENTER_URL":  "https://xx.example.com/ba037",
   "ORDER_DATABASE_CONFIG": {
    "host": "172.21.0.9",
    "port": 3306,
    "user": "root",
    "passwd": "password",
    "db": "database_name"
  },
  "USER": [{"ID": 1, "name": "a"}, {"ID": 2, "NAME": "b"}]
}
```

### PyYAML安装
预知环境：
- python3
- 已安装flask模块  

这里我们直接使用pip来安装即可。
```
[root@VM_122_122_centos ~]# pip install PyYAML
```

### PyYAML使用
安装完成之后，我们在flask框架中的app.py中编写读取yaml文件函数：
```
# 导入yaml 文件
import yaml
# 读取yaml文件函数(path为yaml文件路径)
def read_yaml(path):
    with open(path, 'rb') as f:
        cf= f.read()
    cf = yaml.load(cf)
return cf
```
使flask加载yaml中的配置：
```
from flask import Flask
app = Flask(__name__)
cf = read_yaml("config.yaml")
app.config.update(cf)
```
完整app.py代码：
```
from flask import Flask
from flask_restful import Api
import importlib
import sys
import os
import logging
import yaml
importlib.reload(sys)
sys.path.append(os.path.join(os.path.dirname(os.path.abspath(__file__))))
sys.path.append(os.path.join(os.path.dirname(os.path.abspath(__file__)), '..'))


def create_app():
    app = Flask(__name__)
    # log初始化
    handler = logging.FileHandler(filename='log/flask.log', encoding='utf-8')
    handler.setLevel("DEBUG")
    format_ = "%(asctime)s[%(name)s][%(levelname)s] :%(levelno)s: %(message)s"
    formatter = logging.Formatter(format_)
    handler.setFormatter(formatter)
    app.logger.addHandler(handler)
    return app


def read_yaml(path):
    with open(path, 'rb') as f:
        cf = yaml.safe_load(f.read())
    return cf


app = create_app()
api = Api(app)
app.debug = True
cf = read_yaml("conf.yaml")
app.config.update(cf)
```

### 在flask项目各个模块中使用yaml配置
比如要在数据库连接模块使用yaml中的配置。
我们需要从flask导入current_app，演示如下：
```
# coding=utf-8
import pymysql
from flask import current_app


class DataBaseQa(object):

    def __init__(self):
        #这里获取到yaml中的配置
        db_config = current_app.config['QA_DATABASE_CONFIG']
        db = pymysql.Connect(**db_config)
        self.cursor = db.cursor()

    def query_user_openid(self, userid):
        sql = 'select * from user.users where userid={}'.format(userid)
        self.cursor.execute(sql)
        res = self.cursor.fetchone()
        return res
```
其他模块，同样导入current_app，然后使用current_app.config[]读取配置即可。有关flask框架的current_app详细介绍，以后更新。(可理解为flask的应用实例对象)

## 总结
flask项目中使用yaml的好处：
- yaml编写简单
- 维护与修改配置文件容易
- 更快速的进行环境的切换(比如创建测试环境和开发环境两套yaml配置,在flask启动是读取不同环境配置即可切换)


> 参考文章：
 https://blog.csdn.net/qq_22034353/article/details/88591681

