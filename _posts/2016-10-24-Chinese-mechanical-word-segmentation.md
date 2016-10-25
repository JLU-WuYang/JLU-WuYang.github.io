---
layout: post
title: 中文机械分词
description: "中文机械分词"
modified: 2016-10-24
tags: [NLP,Python]
image:
  feature: abstract-4.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

### 中文分词

下面介绍的是基于字符串匹配的方法的四种算法，正向最大匹配法，逆向最大匹配法，双向最大匹配法和最少切分法。 

基于字符串匹配的方法是按照一定的策略将待分析的汉字串与一个“充分大的”机器词典中的词条进行配，若在词典中找到某个字符串，则匹配成功（识别出一个词）。

从上文介绍可知我们首先要有的是词典。 我采用的是从人民日报1998年中文标注语料库中经过处理得到的词典。

后续改进会使用jieba分词中的词典。

#### 词典的获取

- 语料库文件格式解析

  语料库文件：
  
  <figure>
	<img src="/images/语料库.png" alt="">
  </figure>
  

- 词典格式解析

  词典在内存中key为词，value为词性。其实还应添加词频，jieba词典中就是这样设计，今后会改进。  
  在文件中，第一列为词，第二列为词性。  

- 语料库处理代码
  
  ```python
  class CreateChineseDict(object):
    
    key_dict = {}
    
    def __init__(self, file_list, defaultencoding):
        if len(file_list) == 0 :
            return
        content = ""
        for a_file in file_list: 
            with open(a_file, "r") as f:
                content += f.read()
        rows = content.split()
        temp_dict = {}
        for row in rows:
            items = row.split("/")
            if items[1] in ["m"]:
                continue
            if "[" in items[0]:
                items[0] = items[0][1:] 
            if "]" in items[0]:
                items[0] = items[0][:-1]
            temp_dict[items[0].decode(defaultencoding)] = items[1].decode(defaultencoding)
        temp_key_set = set(temp_dict.keys())
        for key in temp_key_set:
            self.key_dict[key] = temp_dict[key]
        
    def keys(self):
        return self.key_dict.keys()
        
    def save(self, file_name):
        with open(file_name, "w") as f:
            for key in self.keys():            
                f.write(key+" "+self.key_dict[key]+"\n")
                
    def read(self, file_name, defaultencoding):
        with open(file_name, "r") as f:
            rows = f.read().split()
            for i in xrange(0, len(rows), 2):
                self.key_dict[rows[i].decode(defaultencoding)] = rows[i+1].decode(defaultencoding)
        
  ```


#### 正向最大匹配法（由左到右的方向）

- 算法思想

  该算法认为词语颗粒度越大越好，故采取由左到右扫描，由长到短匹配的方法。算法开始由第一个字开始，取前maxLen（事先规定好的长度）的字，
  若其组成的词语在词典中则分出第一个词，下一次扫描从下一个字开始，仍取maxLen的长度。否则，就从本次扫描起点开始取maxLen-1，在词典中继续匹配。
  若到maxLen=1时仍为分出词，则把扫描起点所在的字定为该次扫描分出的词。

- 算法步骤

  <figure>
	<img src="/images/maxSeg.png" alt="">
  </figure>


- 算法python实现

  ```python
  def maxSeg(sentence, dic, maxLen):
    word_list = []
    accuracy_list = []
    length = len(sentence)
    begin = 0
    while begin < length:
        i = maxLen if maxLen + begin < length else length - begin
        flag = False
        while i > 1:
            if begin+i <= length and sentence[begin:begin+i] in dic:
                word_list.append(sentence[begin:begin+i])
                accuracy_list.append((begin, begin+i))
                flag = True
                break
            i = i - 1
        if not flag:
            word_list.append(sentence[begin:begin+i])
            accuracy_list.append((begin, begin+i))
        begin = begin + i  
    return word_list
  ```

#### 逆向最大匹配法（由右到左的方向）

- 算法思想

  逆向最大匹配法与正向最大匹配法大体相同，区别在于后者是从左往右扫描，扫描起点在左侧，前者是从右往前扫描，扫描起点在右侧。

- 算法python实现

  ```python
  def maxRevSeg(sentence, dic, maxLen):
    word_list = []
    accuracy_list = []
    length = len(sentence)
    end = length
    while 0 < end:
        i = maxLen if end - maxLen > 0 else end
        flag = False
        while i > 1:
            if end - i >= 0 and sentence[end-i:end] in dic:
                word_list.append(sentence[end-i:end])
                accuracy_list.append((end-i, end))
                flag = True
                break
            i = i - 1
        if not flag:
            word_list.append(sentence[end-i:end])
            accuracy_list.append((end-i, end))
        end = end - i  
    return word_list
  ```

#### 最少切分法（使每一句中切出的词数最小）

在检索相关资料时，发现机械分词这一类算法中，最少切分算法相关资料很少，仅找到篇由C++实现的代码，而且还没有关于算法的介绍。
下面我就通过对代码的解析，来说明一下算法的思想。

<figure>
	<img src="/images/新文档_1.jpg" alt="">
</figure>
<figure>
	<img src="/images/新文档_2.jpg" alt="">
</figure>
<figure>
	<img src="/images/新文档_3.jpg" alt="">
</figure>

以上是我看博文的笔记，也许你看不太懂，不过不要紧，下面我会详细说明。


- 算法思想
  
  最少切分算法旨在找到一种使切分后词语数最少的切分方法。为此，切分算法把要切分的句子表成切分词图的形式。然后用动态规划的方法，
  求出有向图中的最短路径。

  其中定义了一些量，一个词典中的词语的距离为1，一个单字的距离为1，同一个点之间距离为0。  
  path为二维数组，记录两点是否有边来连接，1表示有，0表示无。 
  min_path为一维数组，记录该点到中点即句末的最短路（最少切分）。   
  很明显min_path[0]为我们要求的结果。   
  road为一维数组，记录最短路上该点的下一个点
  
  对于不同的编程语言实现时要注意编码。用C++实现的可能就要两个来表示一个汉字。我采用python实现，
  并且使用unicode编码直接一个就代表一个汉字。
  
- 算法步骤

  1. 从句子中找到所有在词典中存在的词
  2. 根据句子长度确定path为(length+1)*(length+1)与min_path长度为length+1
  3. 设点为句子各个字的两侧，若两个点之间的字组成的词在字典中，则将两点连接上由小指向大的有向边
  5. 设path均为0，min_path为较大的数可设为1000
  6. 根据动态规划，解得最短路即最少切分
    

- 算法python实现

  ```python
  import numpy as np


  def minSplit(sentence, dic):
    word_list = []
    length = len(sentence) + 1
    path = np.zeros((length,length)) 
    for i in xrange(length):
        for j in xrange(i, length):
            if i == j:
                path[i][j] = 0
            
            else:
                if sentence[i:j] in dic:
                    path[i][j] = 1
    min_path = [10000 for i in xrange(length)]
    road = [0 for i in xrange(length)]
    min_path[length-1] = 0
    for i in xrange(length-1, -1, -1):
        for j in xrange(i, length):
            if path[i][j] == 1 and (min_path[j] + path[i][j] < min_path[i]):
                min_path[i] = min_path[j] + path[i][j]
                road[i] = j
    road.append(0)
    min_road = sorted(list(set(road)))
    for i in xrange(len(min_road)-1):
        word_list.append(sentence[min_road[i]:min_road[i+1]])
    return word_list
  ```     

#### 双向最大匹配法（进行由左到右、由右到左两次扫描）

- 算法思想

  双向最大匹配算法是正向最大匹配算法与逆向最大匹配算法的结合，也可理解为取舍。
  若相同则随意返回一个，若不同则通过下面启发式规则进行评价。

  - 启发式规则
  
    - 总词语个数
    - 非字典词
    - 单字字典词个数

- 关于启发式规则实现的小Tips
  
  一般情况下启发式规则需用硬编码方式实现，我将通过给予不同指标不同系数来实现。

  实现的函数包含两个List参数，一个是指定正向与逆向的maxLen，另一个就是weights。  

  ```python
  if a1 > b1:
      return m
  if a1 < b1:
      return n 
  if a2 > b2:
      return m
  if a2 < b2:
      return n
  if a3 > b3:
      return m
  if a3 < b3:
      return n
  ```
  这么复杂的编码其实就说明一件事a1>a2>a3，这三个指标前者重要程度大于后者。 
  通过weights我们可以简化这个过程。 
  
  ```python
  weights = [1000,1,0.001]
  ```
  只要倍数设置合理，下面的实现完全等价与上面。

- 算法python实现
  
  ```python
  def doubleMatch(sentence, dic, maxLens, weights):
    seg_word = maxSeg(sentence, dic, maxLens[0])
    rev_seg_word = maxRevSeg(sentence, dic, maxLens[1])
    rev_seg_word.reverse()
    is_same = True
    is_same = False if len(seg_word) == len(rev_seg_word) else True
    for i, j in zip(seg_word, rev_seg_word):
        if i != j:
            is_same = False
            break
    if is_same:
        return seg_word
    sum_words = [len(seg_word), len(rev_seg_word)]
    not_in_dic = [0, 0]
    single_word = [0, 0]
    count1 = 0
    count2 = 0
    for word in seg_word:
        if word not in dic:
            count1 += 1
        else:
            if len(word) == 1:
                count2 += 1
    not_in_dic[0] = count1
    single_word[0] = count2
    count1 = 0
    count2 = 0
    for word in rev_seg_word:
        if word not in dic:
            count1 += 1
        else:
            if len(word) == 1:
                count2 += 1
    not_in_dic[1] = count1
    single_word[1] = count2 
    score1 = weights[0] * sum_words[0] + weights[1] * not_in_dic[0] + weights[2] * single_word[0]
    score2 = weights[0] * sum_words[1] + weights[1] * not_in_dic[1] + weights[2] * single_word[1]
    if score1 < score2:
        return seg_word
    else:
        return rev_seg_word
  ```


#### 参考资料

- [TSE中关于分词的算法的改写--最少切分][1]
- [NLP中文信息处理---正向最大匹配法分词][2]
- [中文分词实现——双向最大匹配][3]

[1]:http://www.cnblogs.com/kakamilan/archive/2012/08/04/2623444.html
[2]:http://blog.csdn.net/xn4545945/article/details/8720620
[3]:http://blog.csdn.net/chenlei0630/article/details/40710441
