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

### 创建、访问、修改Dictionary

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

```python
class dic_test(object):


    def __init__(self):    
        self.dic = {}

if __name__ == "__main__":
    test1 = dic_test()
    dic = {}
    dic[test1] = 1
    print dic[test1]
    test1.dic[1] = 1
    print dic[test1]
    
#result
1
1
```

可以看出Dictionary对象是unhashable，但是用类封装Dictionary的对象是hashable。这说明是故意把Dictionary设计成unhashable。在阐述这个原因之前
我们先回答为什么自定义类是hashable的。  

下面是关于hash的官方文档： 

>object.\__hash\__(self)

>Called by built-in function hash() and for operations on members of hashed collections including set, frozenset, and dict. __hash__() should return an integer. The only required property is that objects which compare equal have the same hash value; it is advised to somehow mix together (e.g. using exclusive or) the hash values for the components of the object that also play a part in comparison of objects.

>If a class does not define a \__cmp\__() or \__eq\__() method it should not define a \__hash\__() operation either; if it defines \__cmp\__() or \__eq\__() but not \__hash\__(), its instances will not be usable in hashed collections. If a class defines mutable objects and implements a \__cmp\__() or \__eq\__() method, it should not implement \__hash\__(), since hashable collection implementations require that an object’s hash value is immutable (if the object’s hash value changes, it will be in the wrong hash bucket).

>**User-defined classes have \__cmp\__() and \__hash\__() methods by default; with them, all objects compare unequal (except with themselves) and x.\__hash\__() returns a result derived from id(x).**

>Classes which inherit a \__hash\__() method from a parent class but change the meaning of \__cmp\__() or \__eq\__() such that the hash value returned is no longer appropriate (e.g. by switching to a value-based concept of equality instead of the default identity based equality) can explicitly flag themselves as being unhashable by setting \__hash\__ = None in the class definition. Doing so means that not only will instances of the class raise an appropriate TypeError when a program attempts to retrieve their hash value, but they will also be correctly identified as unhashable when checking isinstance(obj, collections.Hashable) (unlike classes which define their own \__hash\__() to explicitly raise TypeError).

>Changed in version 2.5: \__hash\__() may now also return a long integer object; the 32-bit integer is then derived from the hash of that object.

>**Changed in version 2.6: \__hash\__ may now be set to None to explicitly flag instances of a class as unhashable.**


>hash(object)
 
>Return the hash value of the object (if it has one). Hash values are integers. They are used to quickly compare dictionary keys during  a dictionary lookup. Numeric values that compare equal have the same hash value (even if they are of different types, as is the case for 1 and 1.0).

由文档可知，用户定义的类hash值默认为为id值除以一个数。 

>id定义如下：

>id(object)

>Return the “identity” of an object. This is an integer (or long integer) which is guaranteed to be unique and constant for this object during its lifetime. Two objects with non-overlapping lifetimes may have the same id() value.
CPython implementation detail: This is the address of the object in memory.

hash是主要用来在字典中判断键值是否相等。  
hash()函数（和相关的\__hash\__()方法）用于创建集合中使用的小整数key，如下集合：set、frozenset和dict。  
这些集合使用不可变对象的hash值来快速定位对象。  

所以，由于使用默认的\__hash\__函数，故自定义类为hashable，而且hash值与id值有关。在CPython，id还是与内存地址有关，故
可以解释我们的代码，字典的key内部改变了，仍然判为相等。  
虽然我们用自定义类的方式骗过了解释器，但是不符合设计的思想即判定对象是否相等。 
故还是不用为好。用那些不可变对象作为key。 


关于可变对象与不可变对象的两个例子

```python 
dic = {}
lis = [0]
dic[1] = lis
lis.append(2)
print dic[1]
    
lis1 = [lis]
lis.append(3)
print lis1[0]

#result  
[0, 2]
[0, 2, 3]
```

```python
dic = {}
a = 0
dic[1] = a
a = 2
print dic[1]
    
b = 0
lis = [b]
b = 2
print lis[0]

#result
0
0
```

### 删除字典元素


```python
dic ={1: 2, 3: 4}
dic.clear()
print dic

#result
{}
```

```python
dic ={1: 2, 3: 4}
del dic
print dic

#result
"NameError: name 'dic' is not defined"
```

### 内置函数

- **cmp(dict1, dict2)**  比较两个字典元素(为0时两者相等)
  
  ```python
  dic1 = {1: 2, 3: 4}
  dic2 = {1: 2, 3: 2}
  print cmp(dic1, dic2)
  dic3 = {1: 2, 3: 4}
  print cmp(dic1, dic3)
  dic4 = {1: 2, 3: 5}
  print cmp(dic1, dic4)
  
  #result
  1
  0
  -1
  ```
  
- **len(dict)**  计算字典元素个数，即键的总数
  
  ```python
  dic1 = {1: 2, 3: 4}
  print len(dic1)
  
  #result
  2
  ```
  
- **str(dict)**  输出字典可打印的字符串表示。
  
  ```python
  dic1 = {1: 2, 3: 4}
  print str(dic1)
  
  #result
  {1: 2, 3: 4}
  ```
  
- **dict.fromkeys(seq[, value]))**  创建一个新字典，以序列seq中元素做字典的键，val为字典所有键对应的初始值
  
  ```python
  seq = ('name', 'age', 'sex')

  dict = dict.fromkeys(seq)
  print "New Dictionary : %s" %  str(dict)

  dict = dict.fromkeys(seq, 10)
  print "New Dictionary : %s" %  str(dict)
  
  #result
  New Dictionary : {'age': None, 'name': None, 'sex': None}
  New Dictionary : {'age': 10, 'name': 10, 'sex': 10}
  ```
  
- **dict.get(key, default=None)** 返回指定键的值，如果值不在字典中返回default值
  
  ```python
  dic1 = {1: 2, 3: 4}
  print dic1.get(1, 0)
  print dic1
  print dic1.get(8, 0)
  print dic1
  
  #result
  2
  {1: 2, 3: 4}
  0
  {1: 2, 3: 4}
  ```
  
- **dict.has_key(key)**  如果键在字典dict里返回true，否则返回false
  
  ```python
  dic1 = {1: 2, 3: 4}
  print dic1.has_key(1)
  print dic1.has_key(8)
  
  #result
  True
  False
  ```

- **dict.setdefault(key, default=None)**  和get()类似, 但如果键不存在于字典中，将会添加键并将值设为default
  
  ```python
  dic1 = {1: 2, 3: 4}
  print dic1.setdefault(1, 0)
  print dic1
  print dic1.setdefault(8, 0)
  print dic1
  
  #result
  2
  {1: 2, 3: 4}
  0
  {8: 0, 1: 2, 3: 4}
  ```

- **dict.items()**  以列表返回可遍历的(键, 值) 元组数组
  
  ```python
  dic1 = {1: 2, 3: 4}
  print dic1.items()
  
  #result
  [(1, 2), (3, 4)]
  ```

- **dict.keys()**  以列表返回一个字典所有的键
  
  ```python
  dic1 = {1: 2, 3: 4}
  print dic1.keys()
  
  #result
  [1, 3]
  ```

- **dict.values()**  以列表返回字典中的所有值
  
  ```python
  dic1 = {1: 2, 3: 4}
  print dic1.values()
   
  result:
  [2, 4]
  ```

- **dict.copy()**  	返回一个字典的浅复制
  
  ```python
  dic1 = {1: 2, 3: 4}
  print dic1.copy()
  
  #result
  {1: 2, 3: 4}
  ```
  
- **dict.update(dict2)**  把字典dict2的键/值对更新到dict里

  相同key，更新值  
  
  不同key，把dict中没有的键值对加上  
  
  dict改变，dict2不变
  
  
  ```python
  dic1 = {1: 2, 3: 4}
  dic2 = {4: 5}
  dic1.update(dic2)
  print dic1
  print dic2
  
  #result
  {1: 2, 3: 4, 4: 5}
  {4: 5}
  ```
  
  ```python
  dic1 = {1: 2, 3: 4}
  dic2 = {3: 5}
  dic1.update(dic2)
  print dic1
  print dic2
  
  #result:
  {1: 2, 3: 5}
  {3: 5}
  ```
  
### 参考资料：

- [Python 官方文档-hash()][1]
- [python 官方文档- __hash()__][5]
- [hash()方法介绍][2]
- [Python id函数][3]
- [Python Dictionary教程][4]

[1]:https://docs.python.org/2/library/functions.html#hash
[2]:http://young-py.github.io/python/2015/06/23/python-2-02.html
[3]:http://blog.csdn.net/djskl/article/details/25886187
[4]:http://www.runoob.com/python/python-dictionary.html
[5]:https://docs.python.org/2.7/reference/datamodel.html#object.__hash__
  




