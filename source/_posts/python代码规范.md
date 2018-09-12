---
title: python代码规范
date: 2018-07-09 15:48:47
tags: 
---

```
The Zen of Python
Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to
guess.
There should be one-- and preferably only one --obvious
way to do it.
Although that way may not be obvious at first unless
you're Dutch.
Now is better than never.
Although never is often better than |right| now.
If the implementation is hard to explain, it's a bad
idea.
If the implementation is easy to explain, it may be a
good idea.
Namespaces are one honking great idea -- let's do more
of those!
-- by Tim Peters
```

只记录must部分

 - 禁止使用from xxx import yyy语法直接导入类或函数(即yyy只能是module或
package, 不能是类或函数)。
 
 - 禁止使用from xxx import *

 - import时必须使用package全路径名(相对PYTHONPATH), 禁止使用相对路径(相对当前路径)。
<!-- more -->

 -  禁止使用双参数形式（raise MyException, 'Error Message'）或字符串形式（raise 'Error Message'）语法抛异常。

 - 除非重新抛出异常, 禁止使用except:捕获所有异常。

 - 函数返回值必须小于等于3个。3个以上时必须通过class/namedtuple/dict等具名形式进行包装

 -  对容器类型, 使用in或not in判断元素是否存在。而不是has_key。

 - 仅可使用以下基本类型字符常量或常量作为默认参数：整数、bool、浮点、字符串、None

 -  禁止使用==或!=判断表达式是否为None, 应该用is或is not None

    判断某个整数表达式expr是否为零时, 禁止使用not expr, 应该使用expr == 0

    当明确expr为bool类型时, 禁止使用==或!=与True/False比较。应该替换为expr或not expr

 -  每行只能导入⼀个库
 
    ·[强制] 必须按如下顺序排列import, 每部分之间留一个空格
    • 标准库
    • 第三方库
    • 应用程序自有库 

 - 类(包括异常)名使用首字母大写驼峰式命名

 - ```# -*- coding: utf-8 -*-```

 - 字符串格式化与拼接, 除了a+b这种最简单的情况外, 应该使用%或format格式化字符串。