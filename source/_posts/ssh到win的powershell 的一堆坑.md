---
title: ssh到win的powershell 的一堆坑
date: 2018/12/20 14:09
tags: 
---

记录一些坑

server是一台win10的gpu机器, 要ssh过去做东西而不影响机主.
client也是win10

1. powershell 没法使用conda的activate

<!-- more -->
https://stackoverflow.com/questions/47800794/how-to-activate-different-anaconda-environment-from-powershell

`conda install -n root -c pscondaenvs pscondaenvs`

然而并没卵用



conda 号称4.6版本修正了这个问题, 使用`conda install -c conda-canary conda`安装测试版
然并卵

解决:

先在cmd里面 `activate`之后再进powershell

