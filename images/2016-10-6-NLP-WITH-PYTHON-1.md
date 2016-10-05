## NLP WITH PYTHON NOTEBOOK
### ��һ�� ���Դ�����Python
** Python ���� �� NTLK  **  
####  NTLKʹ���ܽ�
- ��������  
``` python
>>>import nltk
>>>nltk.download()
``` 
- ʹ�������ı�
```python
>>>from nltk.book import *
>>>text1
<Text: Moby Dick by Herman Melville 1851>
```
- �����ı�
```python
>>>text1.concordance("monstrous")
```
- ���������Ƶ������ģ������ƴ�����ʹ�ã�
```python
>>>text1.similar("monstrous")
```
- �������������ϵĴʹ�ͬ��������
```python
>>>text2.common_contexts(["monstrous","very"])
be_glad  am _glad
```
- ����ɢͼ���жϴ����ı��е�λ�ã�����Ϊ���ʣ���Ϊ�����ı���
```python
#�谲װNumpy��Matplotlib��
>>>text4.dispersion_plot(["citizens","freedom"])
```
![dispersion_plot]("dispersion_plot.png")
- �����ʻ�
1.ͳ�ƴʻ����������ظ��ʻ㣩
```python
>>>len(text1)
```
2.ͳ�ƴʻ������������ظ��ģ�
```python
>>>len(set(text1))
```
3.ͳ�ƴʻ������
```python
>>>sorted(set(text1))
```
4.����һ�������ı����ֵĴ���
```python
>>>text3.count("smote")
```
5.�����ı��ʻ�ķḻ�ȣ���ÿ����ƽ����ʹ�õĴ�����
```python
>>>len(text3)/len(set(text3))
#д�ɺ���
def lexical_diversity(text):
        return len(text)/len(set(text))
#����
>>>lexical_diversity(text3)
```
6.����ĳ����ռ�ı�ȫ�������İٷֱ�
```python
>>>100*text4.count('a')/len(text4)
#д�ɺ���
def word_percentage(word,text):
        return 100*text4.count(word)/len(text)
#����
>>>word_percentage('a',text4)
```
7.��ĳ�ʵ�һ�γ��ֵ�������λ�ã�
```python
#Text �����±���� ��text1[0]
#Text ֧����Ƭ�͵���
>>>text4.index('awaken')
```
8.����Ƶ�ʷֲ�
```python
#from nltk.book import *
>>>fdist1=FreqDist(text1)
>>>fdist1
<FreqDist with 260819 outcomes>
>>>fdist1['whale']
906
```
9.����Ƶ��ͼ
```python
>>>fdist1.plot(50,cumulative=True)
>>>fdist1.plot(50,cumulative=False)
```
����plot�����ĺ���
**Parameters:**
- **title (str) **- The title for the graph
- **cumulative** - A flag to specify whether the plot is cumulative (default = False)
- **num (int)** - The maximum number of samples to plot (default=50). Specify num=0 to get all samples (slow).
�ص����һ��**cumulative**�� 
�ò���ָ���Ƿ��ۻ�����Ϊ**True**���samples�ı����ۼ�������ʾ��Ƶ��ͼ�в�����ʾ�����ʵı���������ͼ����Ϊ**False**����ʾ����ʾ�����ʵı���������ͼ�ң�
![cumulative_True](cumulative_True.png) ![cumulative_False](cumulative_False.png)
10.ͨ�����Ⱥ�Ƶ�Ƚ��е���ѡ��
```python
>>>fdist5=FreqDist(text5)
>>>sorted([w for w in set(text5) if len(w)>7 and fdist5[w]>7])
```
11.����
- ˫����
```python
#from nltk import bigrams
#��ע�ⷵ�ص���generator
#������list()Ҳ������next()����
>>>list(bigrams(['more','is','said','than','done']))
```
- ���䣨��������Ƶ����˫���ʣ�
```python
>>>text4.collocations()
```
####FreqDist�ܽ�

| ����        | ����  |
| --------   |::------: |
| fdist = FreqDist(samples)    |  ������������������Ƶ�ʷֲ�|
|fdist.inc(sample)         |   ��������   |
| fdist['monstrous']       |    ���������������ֵĴ���    |
|fdist.freq('monstrous')|����������Ƶ��|
|fdist.N()|��������|
|fdist.most_common(n)|�����n �����������ǵ�Ƶ��|
|for sample in fdist:|��������|
|fdist.max()|��ֵ��������|
|fdist.tabulate()|����Ƶ�ʷֲ���|
|fdist.plot()|����Ƶ�ʷֲ�ͼ|
|fdist.plot(cumulative=True)|�����ۻ�Ƶ�ʷֲ�ͼ|
|fdist1 < fdist2|����������fdist1 �г��ֵ�Ƶ���Ƿ�С��fdist2|

 ####�����ַ������жϺ����ܽ�
| ����        | ����  |
| --------   |::------: |
| s.startswith(t)| 	����s�Ƿ���t ��ͷ| 
| s.endswith(t)| 	����s�Ƿ���t ��β| 
| t in s| 	����t�Ƿ���s���Ӵ�| 
| s.islower()| 	����s�������ַ��Ƿ������С��������ַ����Ҷ���Сд| 
| s.isupper()| 	����s�������ַ��Ƿ������С��������ַ����Ҷ��Ǵ�д| 
| s.isalpha()| 	����s�Ƿ�ǿգ���s�е������ַ�����ĸ| 
| s.isalnum()| 	����s�Ƿ�ǿգ���s�е������ַ�����ĸ������| 
| s.isdigit()| 	����s�Ƿ�ǿգ���s �е������ַ���������| 
| s.istitle()| 	����s�Ƿ������С��������ַ���������ĸ��д������s�����еĴʶ�����ĸ��д��|    

####��ϰ���
- 17��
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
- 18��
```python
sorted(set(sent1+sent2+sent3+sent4+sent5+sent6+sent7+sent8))
"""['!', ',', '-', '.', '1', '25', '29', '61', ':', 'ARTHUR', 'Call', 'Citizens', 'Dashwood', 'Fellow', 'God', 'House', 'I', 'In', 'Ishmael', 'JOIN', 'KING', 'MALE', 'Nov.', 'PMing', 'Pierre', 'Representatives', 'SCENE', 'SEXY', 'Senate', 'Sussex', 'The', 'Vinken', 'Whoa', '[', ']', 'a', 'and', 'as', 'attrac', 'been', 'beginning', 'board', 'clop', 'created', 'director', 'discreet', 'earth', 'encounters', 'family', 'for', 'had', 'have', 'heaven', 'in', 'join', 'lady', 'lol', 'long', 'me', 'nonexecutive', 'of', 'old', 'older', 'people', 'problem', 'seeks', 'settled', 'single', 'the', 'there', 'to', 'will', 'wind', 'with', 'years']"""
```
- 22��
```python
sorted(d.items(),key=lambda x:x[1],reverse=True)
```
- 23��
```python
l=[w for w in text6 if w.isupper()]
for w in l:
        print w
```
- 24��
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
- 25��
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
��Ŀ����[����][1]����
####�ο����ϣ�
- [Class FreqDist][2]

[1]:http://python.usyiyi.cn/documents/nltk_python/ch01.html
[2]:http://jazzparser.granroth-wilding.co.uk/api/nltk.probability.FreqDist-class.html

