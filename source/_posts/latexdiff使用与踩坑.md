---
title: latexdiff使用与踩坑
date: 2018-09-25 10:20:07
tags: 
---
先装CTEX(我是真不想装这个玩意). 装Perl.

miktex的包管理里安装latexdiff

然后就是miktex的各种事, 源要不是过期要不就事未注册. 从miktex的更新文档里面找了个官方源

regedit的
HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\MiKTeX.org\MiKTeX\2.9\MPM\RemoteRepository
的源改为
http://mirror.ctan.org/systems/win32/miktex/tm/packages/

然后挂着梯子(ssr开个全局就行)

安装完后, terminal里面看看latexdiff是不是能够被搜索到, 不能的话手动添环境变量.

```
latexdiff origin.tex modify.tex > diff.tex
```

最后compile这个diff.tex, 最后效果就是

![](http://otivusbsc.bkt.clouddn.com/fe28b16a-a7fd-4aeb-a091-b9b5ca3ebe4a)


