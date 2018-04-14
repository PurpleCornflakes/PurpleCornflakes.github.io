---
layout: post
title: Pandas.DataFrame
category: DataAnalysis
---

## Basic logic

DataFrame is like a dict, but each item is of the same length. And each element in a dict has a index.

Let's look at a `Series` object first.
{% highlight Python linos %}
s = pd.Series(np.random.randn(5), index=['a', 'b', 'c', 'd', 'e'])
'''
a    0.4691
b   -0.2829
c   -1.5091
d   -1.1356
e    1.2121
dtype: float64
'''
s.index
# Index(['a', 'b', 'c', 'd', 'e'], dtype='object')
{% endhighlight %}

A simple `DataFrame`
{% highlight Python linos %}
dates = pd.date_range('20130101',periods=6)
df = pd.DataFrame(np.random.randn(6,4),index=dates,columns=list('ABCD'))
'''
                   A         B         C         D
2013-01-01  0.469112 -0.282863 -1.509059 -1.135632
2013-01-02  1.212112 -0.173215  0.119209 -1.044236
2013-01-03 -0.861849 -2.104569 -0.494929  1.071804
2013-01-04  0.721555 -0.706771 -1.039575  0.271860
2013-01-05 -0.424972  0.567020  0.276232 -1.087401
2013-01-06 -0.673690  0.113648 -1.478427  0.524988
'''
{% endhighlight %}


`DataFrame` from `dict`:
{% highlight Python linos %}
df2 = pd.DataFrame({ 'A' : 1.,
                      'B' : pd.Timestamp('20130102'),
                      'C' : pd.Series(1,index=list(range(4)),dtype='float32'),
                      'D' : np.array([3] * 4,dtype='int32'),
                      'E' : pd.Categorical(["test","train","test","train"]),
                      'F' : 'foo' })
'''
   A          B  C  D      E    F
0  1 2013-01-02  1  3   test  foo
1  1 2013-01-02  1  3  train  foo
2  1 2013-01-02  1  3   test  foo
3  1 2013-01-02  1  3  train  foo
'''
df2.dtypes
''' 
A           float64
B    datetime64[ns]
C           float32
D             int32
E          category
F            object
dtype: object
'''
{% endhighlight %}


## References

For more about:
- Viewing data
- selection elements by label/position

See 
![10 minutes to Pandas](http://pandas.pydata.org/pandas-docs/version/0.15/10min.html)





