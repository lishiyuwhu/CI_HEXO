---
title: Fizz Buzz
date: 2017-11-22 15:43:09
tags:
---

Fizz Buzz算是我学数学还没转行时, 学编程经常看到的一个问题. 然后印象深刻的是这个答案:

```python
for x in range(101): print('fuzz'[x%3*4::] + 'buzz'[x%5*4] or x)
```

今天在MLLecture 9-3看到个 fizzbuzz的笑话, http://joelgrus.com/2016/05/23/fizz-buzz-in-tensorflow/,准备拿tensorlayer也来做一下😀