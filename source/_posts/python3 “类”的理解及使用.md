---
title: python3 “类”的理解及使用
date: 2019-08-23 16:00:43
categories: "python"
tags: 
  - 技术
  - python
toc: true
---

## 前言
在设计测试平台初期，框架及代码设计的一坨屎。各种复杂、重复的设计。没有做到极简。总结原因，感觉还是由于基础不够扎实所致。如：
- 类实例具体是如何初始化的？
- 类方法的__init__改如何理解并使用？
- 类方法的__new__改如何使用？
- 类方法的__close__方法？
- 类的私有变量及方法变量如何规范使用？
- 等等诸如此类的问题。  
俗话说“基础不牢，地动山摇”。所以重新整理下一些基础知识。会更有利于设计成熟且赏心悦目的工程。那么接下来，就一一解答下关于“类”的设计吧。

## 类的方法
类的方法有三种，实例方法，静态方法，类方法。
> 补充：python中，cls、self参数的区别：  
实例方法中，第一个参数需要是self，代表具体实例本身。  
如果用了@staticmethod，可以无视self参数，即当成普通函数使用。  
如果用了@classmethod，首个参数是cls，而不是self，代表类本身。

### 实例方法
实例方法只能被实例对象调用。  
code：  
```
# coding:utf-8
class Foo(object):
    """类三种方法语法形式"""
 
    def instance_method(self):
        print("是类{}的实例方法，只能被实例对象调用".format(Foo))

# 正确调用
foo = Foo()
foo.instance_method()
print('------------------')
# 错误调用(不能直接调用)
Foo.instance_method()
```
运行结果如下：  
```
是类<class '__main__.Foo'>的实例方法，只能被实例对象调用
------------------
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unbound method instance_method() must be called with Foo instance as first argument (got nothing instead)
```

### 类的实例化顺序
>  在Python3中所有的类均默认继承object，所以并不需要显式地指定object为基类。  
以object为基类可以使得所定义的类具有新类所对应的方法（methods）和属性（properties）。

1. 调用__new__生成实例（constructor）
2. 调用__init__初始化实例（initializer）
3.

#### __new__方法
__new__所接受的第一个参数是cls,作为构造器，作用是用来创造实例。
- __new__函数直接上可以返回别的类的实例

#### __init__方法
__init__方法所接受的第一个参数是self，作为初始化器，作用初始化实例本身。
- __init__方法不能有返回值

#### code
```
class newStyleClass(object): 

    def __new__(cls):
        print("__new__ is called")
        return super(newStyleClass, cls).__new__(cls)

    def __init__(self):
        print("__init__ is called")
        print("self is: ", self)

newStyleClass()
```
执行结果：
```
__new__ is called
__init__ is called
('self is: ', <__main__.newStyleClass object at 0x100b11c90>)
<__main__.newStyleClass object at 0x100b11c90>
```

由以上代码可见 类的实例化流程，__new__函数首先被调用，生成一个newStyleClass实例。并且将此实例return给用__init__方法来初始化，这时候类实例作为self参数传入__init__函数。  
**由此引申一下：**  
假如__new__方法retun 的是一个已存在的实例，那么__init__方法就不会被调用。我们现在来验证一下：  
code：  
```
# 构造一个已存在的对象
exist_obj = 1 

class TestzClass(object):
    def __new__(cls):
        print("__new__ is called")
        # 返回一个已存在的实例对象
        return exist_obj

    def __init(self):
        print("__init__ is called")

TestzClass()
```
执行结果：
```
__new__ is called
1
```
因此，我们可以用这个特性来构造一个单例模式。
比如：
```
# coding=utf-8
# 单利模式实现
class Singleton(object):
    _instance = None
    def __new__(cls, *args, **kwargs):
        if not cls._instance:
            # 因为Singleton的__new__方法已修改，被用来判断是否有实例了，这一步通过调用夫类object的__new__方法来实现生成实例
    	    cls._instance = super(Singleton, cls).__new__(cls, *args, **kwargs)
        return cls._instance

# 实例化两个对象        
obj_a = Singleton()
obj_b = Singleton()
# 单例验证
print(obj_a, obj_b)
print(obj_a is obj_b)
```
执行结果：
```
(<__main__.Singleton object at 0x1022d69d0>, <__main__.Singleton object at 0x1022d69d0>)
True
```
可以看到两个实例的内存地址是一致的。其他单例模式实现到时候会有单独再更新。