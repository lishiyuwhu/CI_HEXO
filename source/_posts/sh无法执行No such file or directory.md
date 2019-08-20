---
title: .sh无法执行No such file or directory
date: 2019/08/20 20:50:42
tags: 
---

执行.sh报错No such file or directory

<!-- more -->

首先给权限 `chmod u+x filename `
然后vim里面看`:set ff  `  , 返回的fileformat是不是unix
不是的话`:set ff=unix`设置为unix就能够执行了
