---
layout: post
title: NLP WITH PYTHON 1
description: "NLP WITH PYTHON的读书笔记"
modified: 2016-10-06
tags: [NLP,Python,读书笔记]
image:
  feature: abstract-3.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---
## NLP WITH PYTHON NOTEBOOK
### 第一章 语言处理与Python
**Python 环境 ： NTLK**  
####  NTLK使用总结
- 下载数据  
``` python
>>>import nltk
>>>nltk.download()
``` 
- 使用下载文本
```python
>>>from nltk.book import *
>>>text1
<Text: Moby Dick by Herman Melville 1851>
```
- 搜索文本
```python
>>>text1.concordance("monstrous")
```
- 出现在相似的上下文（即相似搭配中使用）
```python
>>>text1.similar("monstrous")
```
- 两个或两个以上的词共同的上下文
```python
>>>text2.common_contexts(["monstrous","very"])
be_glad  am _glad
```
- 画离散图来判断词在文本中的位置（竖线为单词，行为整个文本）
```python
#需安装Numpy的Matplotlib包
>>>text4.dispersion_plot(["citizens","freedom"])
```
![dispersion_plot]("../img/dispersion_plot.png")
- 计数词汇
1.统计词汇数（计入重复词汇）
```python
>>>len(text1)
```
2.统计词汇数（不计入重复的）
```python
>>>len(set(text1))
```
3.统计词汇项并排序
```python
>>>sorted(set(text1))
```
4.计数一个词在文本出现的次数
```python
>>>text3.count("smote")
```
5.测量文本词汇的丰富度（即每个字平均被使用的次数）
```python
>>>len(text3)/len(set(text3))
#写成函数
def lexical_diversity(text):
        return len(text)/len(set(text))
#调用
>>>lexical_diversity(text3)
```
6.计数某个词占文本全部次数的百分比
```python
>>>100*text4.count('a')/len(text4)
#写成函数
def word_percentage(word,text):
        return 100*text4.count(word)/len(text)
#调用
>>>word_percentage('a',text4)
```
7.找某词第一次出现的索引（位置）
```python
#Text 可用下标访问 如text1[0]
#Text 支持切片和迭代
>>>text4.index('awaken')
```
8.计算频率分布
```python
#from nltk.book import *
>>>fdist1=FreqDist(text1)
>>>fdist1
<FreqDist with 260819 outcomes>
>>>fdist1['whale']
906
```
9.绘制频率图
```python
>>>fdist1.plot(50,cumulative=True)
>>>fdist1.plot(50,cumulative=False)
```
解释plot参数的含义
**Parameters:**
- **title (str)**- The title for the graph
- **cumulative** - A flag to specify whether the plot is cumulative (default = False)
- **num (int)** - The maximum number of samples to plot (default=50). Specify num=0 to get all samples (slow).
重点解释一下**cumulative**： 
该参数指明是否累积，若为**True**则把samples的比例累加起来显示，频率图中不会显示单个词的比例，如下图左；若为**False**则显示会显示单个词的比例，如下图右；
![cumulative_True](../img/cumulative_True.png) ![cumulative_False](../img/cumulative_False.png)  

10.通过长度和频度进行单词选择
```python
>>>fdist5=FreqDist(text5)
>>>sorted([w for w in set(text5) if len(w)>7 and fdist5[w]>7])
```
11.搭配
- 双连词
```python
#from nltk import bigrams
#并注意返回的是generator
#可以用list()也可以用next()方法
>>>list(bigrams(['more','is','said','than','done']))
```
- 搭配（基本上是频繁的双连词）
```python
>>>text4.collocations()
```
####FreqDist总结

| 例子        | 描述  |
| --------   |------|
| fdist = FreqDist(samples)    |  创建包含给定样本的频率分布|
|fdist.inc(sample)         |   增加样本   |
| fdist['monstrous']       |    计数给定样本出现的次数    |
|fdist.freq('monstrous')|给定样本的频率|
|fdist.N()|样本总数|
|fdist.most_common(n)|最常见的n 个样本和它们的频率|
|for sample in fdist:|遍历样本|
|fdist.max()|数值最大的样本|
|fdist.tabulate()|绘制频率分布表|
|fdist.plot()|绘制频率分布图|
|fdist.plot(cumulative=True)|绘制累积频率分布图|
|fdist1 < fdist2|测试样本在fdist1 中出现的频率是否小于fdist2|
 
 
####关于字符串的判断函数总结
| 函数        | 含义  |
| --------   |------|
| s.startswith(t)| 	测试s是否以t 开头| 
| s.endswith(t)| 	测试s是否以t 结尾| 
| t in s| 	测试t是否是s的子串| 
| s.islower()| 	测试s中所有字符是否包含大小有区别的字符，且都是小写| 
| s.isupper()| 	测试s中所有字符是否包含大小有区别的字符，且都是大写| 
| s.isalpha()| 	测试s是否非空，且s中的所有字符是字母| 
| s.isalnum()| 	测试s是否非空，且s中的所有字符是字母或数字| 
| s.isdigit()| 	测试s是否非空，且s 中的所有字符都是数字| 
| s.istitle()| 	测试s是否包含大小有区别的字符，且首字母大写（即，s中所有的词都首字母大写）|    

####练习解答
- 17、
``` python
>>>text9.index("sunset")
>>>s=text9[600:650]
for i in xrange(len(s)):
    if s[i]=='.' or s[i]==';':
      print i,s[i]
#6 . 8 . 10 . 12 . 43 .
>>>example=text9[613:643]
>>>s=""
for i in example:
  s+=" "+i
"""u' CHAPTER I THE TWO POETS OF SAFFRON PARK THE suburb of Saffron Park lay on the sunset side of London , as red and ragged as a cloud of sunset'
"""
```
- 18、
```python
sorted(set(sent1+sent2+sent3+sent4+sent5+sent6+sent7+sent8))
"""['!', ',', '-', '.', '1', '25', '29', '61', ':', 'ARTHUR', 'Call', 'Citizens', 'Dashwood', 'Fellow', 'God', 'House', 'I', 'In', 'Ishmael', 'JOIN', 'KING', 'MALE', 'Nov.', 'PMing', 'Pierre', 'Representatives', 'SCENE', 'SEXY', 'Senate', 'Sussex', 'The', 'Vinken', 'Whoa', '[', ']', 'a', 'and', 'as', 'attrac', 'been', 'beginning', 'board', 'clop', 'created', 'director', 'discreet', 'earth', 'encounters', 'family', 'for', 'had', 'have', 'heaven', 'in', 'join', 'lady', 'lol', 'long', 'me', 'nonexecutive', 'of', 'old', 'older', 'people', 'problem', 'seeks', 'settled', 'single', 'the', 'there', 'to', 'will', 'wind', 'with', 'years']"""
```
- 22、
```python
sorted(d.items(),key=lambda x:x[1],reverse=True)
```
- 23、
```python
l=[w for w in text6 if w.isupper()]
for w in l:
        print w
```
- 24、
```python
>>> s=[w for w in text6 if w.endswith("ize")]
>>> s
[]
>>> s=[w for w in text6 if 'z' in w]
>>> s
[u'zone', u'amazes', u'Fetchez', u'Fetchez', u'zoop', u'zoo', u'zhiv', u'frozen', u'zoosh']
>>> s=[w for w in text6 if 'pt' in w]
>>> s
[u'empty', u'aptly', u'Thpppppt', u'Thppt', u'Thppt', u'empty', u'Thppppt', u'temptress', u'temptation', u'ptoo', u'Chapter', u'excepting', u'Thpppt']
>>> s=[w for w in text6 if w.istitle()]
```
- 25、
```python
>>> sent=['she','sells','sea','shells','by','the','sea','shore']
>>> sent
['she', 'sells', 'sea', 'shells', 'by', 'the', 'sea', 'shore']
>>> s=[w for w in sent if w.startswith("sh")]
>>> s
['she', 'shells', 'shore']
>>> s=[w for w in sent if len(w)>4]
>>> s
['sells', 'shells', 'shore']
```
题目可在[这里][1]访问
####参考资料：
- [Class FreqDist][2]

[1]:http://python.usyiyi.cn/documents/nltk_python/ch01.html
[2]:http://jazzparser.granroth-wilding.co.uk/api/nltk.probability.FreqDist-class.html

