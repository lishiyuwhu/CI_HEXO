---
title: 解决tmux与使用cmder ssh后的冲突问题
date: 2018/12/20 13:48
tags: 
---

这个问题其实挺久了, cmder中ssh到别的机器之后开启tmux, 会有一大堆的排版错误.

解决办法:

(要先开启WSL)

`bash` 后再去ssh, 通过WSL去ssh字节缓冲就是正常的了.

-------------------

啥时候powershell也能有个类似tmux的东西. 现在先ssh到个linux上, 开tmux, 再一个个窗口ssh到windows机器, 简直多此一举