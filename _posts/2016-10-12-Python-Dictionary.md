---
layout: post
title: Python  Dictionary
description: "介绍Python的数据结构Dictionary的使用"
modified: 2016-10-12
tags: [Python]
image:
  feature: abstract-2.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

## Python 之 Dictionary

### 创建Dictionary

字典中数据是用键值对方式（key: value）存储的。 

```python
#key： （hashable）Numbers, Strings, 自定义的object实例
#value: 任意类型

dic_1 = {"1": "a", "2": "b"}
dic_2 = {"a": "1", 1: "c"}
dic_3 = {1: [1, 2]}

class o(object):
    pass
>>>o1 = o()
>>>dic = {}
>>>dic[o1] = 2
>>>dic[o1]
2

#不能用Dictionary
>>>dic [{1: 2}] = 1
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unhashable type: 'dict'


#不能用List作为key
>>>dic ={[1]: 2}
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unhashable type: 'list'
```

根据上述可知List和Dictionary类型对象不能用做key，原因是unhashable。 
那，为什么自定义的o类可以呢？亦或是我把List和Dictionary变成hashable，是不是就可以当key了呢？

```python
class dic_test(object):


    def __init__(self):    
        self.dic = {}

if __name__ == "__main__":
    test1 = dic_test()
    dic = {}
    dic[test1] = 1
    print dic[test1]  
```

上述代码成功运行并输出 1 。

再看下面代码。

```python
#计算hash

In [2]: test1
Out[2]: <__main__.dic_test at 0xbf7bdb0>

In [3]: hash(test1)
Out[3]: 12549083

In [4]: dic = {}

In [5]: hash(dic)
Traceback (most recent call last):

  File "<ipython-input-6-c92042dd4b77>", line 1, in <module>
    hash(dic)

TypeError: unhashable type: 'dict'
```






