---
layout: post
title: NLP WITH PYTHON 2
description: "NLP WITH PYTHON的读书笔记"
modified: 2016-10-06
tags: [NLP,Python,读书笔记]
image:
  feature: abstract-6.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

####**Module :   nltk.text**

####**File :   text.py**


**一直更新中** 


```python
@python_2_unicode_compatible
class Text(object):
    """
    A wrapper around a sequence of simple (string) tokens, which is
    intended to support initial exploration of texts (via the
    interactive console).  Its methods perform a variety of analyses
    on the text's contexts (e.g., counting, concordancing, collocation
    discovery), and display the results.  If you wish to write a
    program which makes use of these analyses, then you should bypass
    the ``Text`` class, and use the appropriate analysis function or
    class directly instead.

    A ``Text`` is typically initialized from a given document or
    corpus.  E.g.:

    >>> import nltk.corpus
    >>> from nltk.text import Text
    >>> moby = Text(nltk.corpus.gutenberg.words('melville-moby_dick.txt'))

    """
    # This defeats lazy loading, but makes things faster.  This
    # *shouldn't* be necessary because the corpus view *should* be
    # doing intelligent caching, but without this it's running slow.
    # Look into whether the caching is working correctly.
    _COPY_TOKENS = True

    def __init__(self, tokens, name=None):
        #要求tokens为字符或单词的一个序列
        #e.g Text("hello"),Text(["my","name","is","Albert"])
        """
        Create a Text object.

        :param tokens: The source text.
        :type tokens: sequence of str
        """
        if self._COPY_TOKENS:
            #转换为列表
            tokens = list(tokens)
        #把内容存储到tokens中
        #这样，把一些比如检索计数的工作就交给list本身处理
        self.tokens = tokens
        #如果给name就设为name
        if name:
            self.name = name
        #否则
        #如果"]"在列表前20个元素
        #另: a.join(b)为用a来分割b的每一个元素
        elif ']' in tokens[:20]:
            #则把"]"之前的单词取出设为name，"["除外（默认第一个为"["）
            end = tokens[:20].index(']')
            self.name = " ".join(text_type(tok) for tok in tokens[1:end])
        #如果没有"]"的话
        else:
            #取前8个单词设为name
            self.name = " ".join(text_type(tok) for tok in tokens[:8]) + "..."

    #////////////////////////////////////////////////////////////
    # Support item & slice access
    #////////////////////////////////////////////////////////////

    #这里定义用[]取元素时的行为
    #e.g Text()[0]
    #e.g Text()[1:5]

    def __getitem__(self, i):
        #如果为切片操作的话，对应第二个例子
        if isinstance(i, slice):
            return self.tokens[i.start:i.stop]
        #对应第一个例子
        else:
            return self.tokens[i]

    def __len__(self):
        return len(self.tokens)

    #////////////////////////////////////////////////////////////
    # Interactive console methods
    #////////////////////////////////////////////////////////////

    def concordance(self, word, width=79, lines=25):
        """
        Print a concordance for ``word`` with the specified context window.
        Word matching is not case-sensitive.
        :seealso: ``ConcordanceIndex``
        """
        if '_concordance_index' not in self.__dict__:
            #print("Building index...")
            self._concordance_index = ConcordanceIndex(self.tokens,
                                                       key=lambda s:s.lower())

        self._concordance_index.print_concordance(word, width, lines)

    def collocations(self, num=20, window_size=2):
        """
        Print collocations derived from the text, ignoring stopwords.

        :seealso: find_collocations
        :param num: The maximum number of collocations to print.
        :type num: int
        :param window_size: The number of tokens spanned by a collocation (default=2)
        :type window_size: int
        """
        if not ('_collocations' in self.__dict__ and self._num == num and self._window_size == window_size):
            self._num = num
            self._window_size = window_size

            #print("Building collocations list")
            from nltk.corpus import stopwords
            ignored_words = stopwords.words('english')
            finder = BigramCollocationFinder.from_words(self.tokens, window_size)
            finder.apply_freq_filter(2)
            finder.apply_word_filter(lambda w: len(w) < 3 or w.lower() in ignored_words)
            bigram_measures = BigramAssocMeasures()
            self._collocations = finder.nbest(bigram_measures.likelihood_ratio, num)
        colloc_strings = [w1+' '+w2 for w1, w2 in self._collocations]
        print(tokenwrap(colloc_strings, separator="; "))

    def count(self, word):
        """
        Count the number of times this word appears in the text.
        """
        return self.tokens.count(word)

    def index(self, word):
        """
        Find the index of the first occurrence of the word in the text.
        """
        return self.tokens.index(word)

    def readability(self, method):
        # code from nltk_contrib.readability
        raise NotImplementedError

    def similar(self, word, num=20):
        """
        Distributional similarity: find other words which appear in the
        same contexts as the specified word; list most similar words first.

        :param word: The word used to seed the similarity search
        :type word: str
        :param num: The number of words to generate (default=20)
        :type num: int
        :seealso: ContextIndex.similar_words()
        """
        if '_word_context_index' not in self.__dict__:
            #print('Building word-context index...')
            self._word_context_index = ContextIndex(self.tokens,
                                                    filter=lambda x:x.isalpha(),
                                                    key=lambda s:s.lower())

#        words = self._word_context_index.similar_words(word, num)

        word = word.lower()
        wci = self._word_context_index._word_to_contexts
        if word in wci.conditions():
            contexts = set(wci[word])
            fd = Counter(w for w in wci.conditions() for c in wci[w]
                          if c in contexts and not w == word)
            words = [w for w, _ in fd.most_common(num)]
            print(tokenwrap(words))
        else:
            print("No matches")


    def common_contexts(self, words, num=20):
        """
        Find contexts where the specified words appear; list
        most frequent common contexts first.

        :param word: The word used to seed the similarity search
        :type word: str
        :param num: The number of words to generate (default=20)
        :type num: int
        :seealso: ContextIndex.common_contexts()
        """
        if '_word_context_index' not in self.__dict__:
            #print('Building word-context index...')
            self._word_context_index = ContextIndex(self.tokens,
                                                    key=lambda s:s.lower())

        try:
            fd = self._word_context_index.common_contexts(words, True)
            if not fd:
                print("No common contexts were found")
            else:
                ranked_contexts = [w for w, _ in fd.most_common(num)]
                print(tokenwrap(w1+"_"+w2 for w1,w2 in ranked_contexts))

        except ValueError as e:
            print(e)

    def dispersion_plot(self, words):
        """
        Produce a plot showing the distribution of the words through the text.
        Requires pylab to be installed.

        :param words: The words to be plotted
        :type words: list(str)
        :seealso: nltk.draw.dispersion_plot()
        """
        from nltk.draw import dispersion_plot
        dispersion_plot(self, words)

    def plot(self, *args):
        """
        See documentation for FreqDist.plot()
        :seealso: nltk.prob.FreqDist.plot()
        """
        self.vocab().plot(*args)

    def vocab(self):
        """
        :seealso: nltk.prob.FreqDist
        """
        if "_vocab" not in self.__dict__:
            #print("Building vocabulary index...")
            self._vocab = FreqDist(self)
        return self._vocab

    def findall(self, regexp):
        """
        Find instances of the regular expression in the text.
        The text is a list of tokens, and a regexp pattern to match
        a single token must be surrounded by angle brackets.  E.g.

        >>> print('hack'); from nltk.book import text1, text5, text9
        hack...
        >>> text5.findall("<.*><.*><bro>")
        you rule bro; telling you bro; u twizted bro
        >>> text1.findall("<a>(<.*>)<man>")
        monied; nervous; dangerous; white; white; white; pious; queer; good;
        mature; white; Cape; great; wise; wise; butterless; white; fiendish;
        pale; furious; better; certain; complete; dismasted; younger; brave;
        brave; brave; brave
        >>> text9.findall("<th.*>{3,}")
        thread through those; the thought that; that the thing; the thing
        that; that that thing; through these than through; them that the;
        through the thick; them that they; thought that the

        :param regexp: A regular expression
        :type regexp: str
        """

        if "_token_searcher" not in self.__dict__:
            self._token_searcher = TokenSearcher(self)

        hits = self._token_searcher.findall(regexp)
        hits = [' '.join(h) for h in hits]
        print(tokenwrap(hits, "; "))

    #////////////////////////////////////////////////////////////
    # Helper Methods
    #////////////////////////////////////////////////////////////

    _CONTEXT_RE = re.compile('\w+|[\.\!\?]')
    def _context(self, tokens, i):
        """
        One left & one right token, both case-normalized.  Skip over
        non-sentence-final punctuation.  Used by the ``ContextIndex``
        that is created for ``similar()`` and ``common_contexts()``.
        """
        # Left context
        j = i-1
        while j>=0 and not self._CONTEXT_RE.match(tokens[j]):
            j -= 1
        left = (tokens[j] if j != 0 else '*START*')

        # Right context
        j = i+1
        while j<len(tokens) and not self._CONTEXT_RE.match(tokens[j]):
            j += 1
        right = (tokens[j] if j != len(tokens) else '*END*')

        return (left, right)

    #////////////////////////////////////////////////////////////
    # String Display
    #////////////////////////////////////////////////////////////

    #print 时显示的内容
    def __str__(self):
        return '<Text: %s>' % self.name
    #创造实例后命令行显示的内容
    def __repr__(self):
        return '<Text: %s>' % self.name
```


- Text的 __init__   

```python
>>> k=Text("ss")
>>> [d for d in k]
['s', 's']
>>> k=Text(["My","name","is","Albert"])
>>> [d for d in k]
['My', 'name', 'is', 'Albert']
``` 

- slice  

```python
>>> c=slice(1,3,1)
>>> s[c]
[2, 3]
```

用一个类进一步说明

```python
class text(object):
    def __init__(self,lis):
           self.content=lis
    def __getitem__(self,i):
           print i

>>> s=text([1,2,3,4,5])
>>> s[1:3]
slice(1, 3, None)
>>> s[1]
1
```

- text_type 

直接用会出错，需

```python
import nltk.text_type
```

但是，

```python
>>> type(text_type("qq"))
<type 'unicode'>
>>> nltk.text_type
<type 'unicode'>
```

原因是：

**compat.py** 

```python
text_type = str
```

