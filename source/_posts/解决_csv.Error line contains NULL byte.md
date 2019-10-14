---
title: 解决_csv.Error line contains NULL byte
date: 2019/9/28 21:01
tags: 
---


```python
fi = open('dev.tsv', 'rb')
data = fi.read()
fi.close()
fo = open('dev', 'wb')
fo.write(data.replace(b'\x00', b''))
fo.close()

```

<!-- more -->
