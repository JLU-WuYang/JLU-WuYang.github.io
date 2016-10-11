---
layout: post
title: Python  List
description: "介绍Python的数据结构List的使用"
modified: 2016-10-12
tags: [Python]
image:
  feature: abstract-1.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---


## Python之List

### 创建List

```python
list1 = ['physics', 'chemistry', 1997, 2000]
list2 = [1, 2, 3, 4, 5 ]
list3 = ["a", "b", "c", "d"]
```

### 访问List中元素

- 正序（单个访问）

```python
>>>list1[0]
physics
```
- 正序（切片访问）

  包括开头不包括结束

  默认步长为 1 

  List[begin : end : step]

  **begin** 默认为 0

  **end**   默认为结尾

  **step**  默认为 1


```python
>>>list2[0:]
[1, 2, 3, 4, 5]
>>>list2[:]
[1, 2, 3, 4, 5]
>>>list2[1:3]
[2, 3]
>>>list2[::2]
[1, 3, 5]
```
- 逆序（单个访问）

```python
>>>list2[-1]
5
```

- 逆序（切片访问）

```python
>>>list2[:-2]
[1, 2, 3]
```

### 更改List中元素

```python
list = ['physics', 'chemistry', 1997, 2000];
print "Value available at index 2 : "
print list[2];
list[2] = 2001;
print "New value available at index 2 : "
print list[2];
```

```python
Value available at index 2 :
1997
New value available at index 2 :
2001
```

### 删除List中元素

```python
list1 = ['physics', 'chemistry', 1997, 2000];
print list1;
del list1[2];
print "After deleting value at index 2 : "
print list1;
```

```python
['physics', 'chemistry', 1997, 2000]
After deleting value at index 2 :
['physics', 'chemistry', 2000]
```

### 求List的长度

```python
>>>list1 = [1,2,3,4,5]
>>>len(list1)
5
```

### 两个List合并为一个List

```python
# + 生成新的列表（前者与后者均不变）
# extend 把后面的列表接到前面的列表（前者改变，后者不变）
>>>list_a = [1, 2, 3]
>>>list_b = [2, 3, 4]
>>>list_a + list_b
[1, 2, 3, 2, 3, 4]
>>>list_a.extend(list_b)
>>>list_a
[1, 2, 3, 2, 3, 4]
```

### 对List进行重复

```python
>>>[1, 2] * 4
[1, 2, 1, 2, 1, 2, 1, 2]
```

### 判断元素是否存在于List中

```python
>>>3 in [1, 2, 3]
True
>>>4 in [1, 2, 3]
False
```

### 对List中元素进行迭代

```python
for i in [1, 2, 3]:
    print i
1
2
3
```

### List的函数

- **cmp(list1, list2)** 比较两个列表的元素

  ```python
  >>>a = [1, 2]
  >>>b = [1, 3]
  >>>cmp(a, b)
  -1
  >>>c = [1, 2]
  >>>cmp(a, c)
  0
  ```
  
- **max(list)** 返回列表元素最大值
  
  ```python
  >>>max([1, 2, 3, 4])
  4
  ```
  
- **min(list)** 返回列表元素最小值

  ```python
  >>>min([1, 2, 3, 4])
  1
  ```
  
- **list(seq)** 将元组转换为列表
 
  ```python
  >>> list((1, 2))
  [1, 2]
  ```
    
- **list.append(obj)** 在列表末尾添加新的对象

  ```python
  >>>list1 = [1, 2]
  >>>list1.append(3)
  >>>list1
  [1, 2, 3]
  ```
  
- **list.count(obj)** 统计某个元素在列表中出现的次数

  ```python
  >>>[1, 1, 3, 4, 4].count(4)
  2
  ```
  
- **list.index(obj)** 从列表中找出某个值第一个匹配项的索引位置

  ```python
  >>>[1, 1, 3, 4, 4].index(4)
  3
  ```
  
- **list.insert(index, obj)** 将对象obj插入列表的index位置元素的前面

  ```python
  >>>list = [1, 3, 2]
  >>>list.insert(0, 5)
  >>>list
  [5, 1, 3, 2]
  ```
  
- **list.remove(obj)** 移除列表中某个值的第一个匹配项

  ```python
  >>>list
  [5, 1, 3, 2]
  >>>list.remove(3)
  >>>list
  [5, 1, 2] 
  
  >>>list3 = [1, 2, 1]
  >>>list3.remove(1)
  >>>list3
  [2, 1]
 
  ```
  
- **list.pop(index)** 移除列表中相应位置的元素，并且返回该元素的值

  ```python
  >>>list3 = [1, 2, 1]
  >>>list3.pop(1)
  2
  >>>list3
  [1, 1]
  ```
  
- **list.reverse()** 反向列表中元素

  ```python
  >>>list3 = [1, 2, 3]
  >>>list3.reverse()
  >>>list3
  [3, 2, 1]
  ```
  
- **list.sort([func])** 对原列表进行排序

  ```python
  >>>list1 = [1, 5, 2, 4, 5]
  >>>list1.sort()
  >>>list1
  [1, 2, 4, 5, 5]
  
  >>>list1
  [1, 5, 2, 4, 5]
  >>>sorted(list1)
  [1, 2, 4, 5, 5]
  >>>list1
  [1, 5, 2, 4, 5]
  ```
  
### Tips

List模块的函数会改变List自身，而 + , sorted() 等不会改变List自身，会返回一个新的List。
  
  
  
  
  
  









