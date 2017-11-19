---
title: PRML NOTES
date: 2017-07-22 19:40:23
tags: 
- PRML
- NOTES
---

为什么不是ESL or MLAPP之类的, 纯粹因为我之前Bayes方面接触的不太多. 本科的时候就经常听到PRML大名, 正好也有译版.(ps.当年那篇翻译版声明的写的是有水平)

---

## 概率论
error function : 通常来说MSE是有效的$E(\omega) = \frac{1}{2}\sum^N_{n-1}\{y(x_n,\vec\omega)-t_n\}^2$

关于高次插值造成的over-fitting : 我想起来了类似的一个[Runge现象](https://en.wikipedia.org/wiki/Runge%27s_phenomenon):高次多项式等距插值在边缘点出现误差无穷大的情况. 事实上, Runge现象是由误差累计传播导致, 和over-fitting是不一样的(说到这里, 当年做Runge这个topic的时候还对比了Fourier analysis里面Gibbs现象)

regularization : 事实上 $L_1$norm是加了一个拉普拉斯先验, $L_2$加了一个高斯先验. 后面应该会有推导.

shrinkage : 这个shrinkage和Stein's Pheonomenon的应该是一个吧?高维情形下,  regularization导致估计量的shrinkage, 进而导致variance的减小, 补偿了bias,  从而使得泛化误差的减小.

<!-- more -->

---
## 信息论
entropy : $H[x]=-\sum_x p(x)\log_2p(x)$
KL divergence : $KL(p||q) = -\int p(x) \ln \{\frac{q(x)}{p(x)}\}$ 
divergence这方面, KL JSD都有一些不好的性质.

---
## exercise
1. 直接(1.1)带入(1.2)然后分别对$\omega_k$求微分  <\br>
2. 和1一样作, $A$变为$A+\lambda I$.  
3. 古典概型$p(P)=0.34$  
    Bayes $p(g|J) = \frac{p(J|g)p(g)}{p(J)} = 0.5$
4. 这个我证的麻烦了, 答案写的很好, 加个$s$取$-1,1$就完事了, 还给了个实例, 可以看看  
5. $E^2(f-Ef) = E(f^2 + E^2f -2f\cdot Ef) $拆出来, 注意$Ef$是个constant  
6. $x,y$独立, 即$p(x,y)=p(x)p(y)$  
7. 按照题目极坐标代换即可, 注意Jacobi阵别写错, 第一遍写错了算出来我都惊了
8. 记得好像是化为标准正态吧? 直接跳过了
9. 一维情形直接导, 多维, 忘了, 翻课本看了一遍
10. 和6一样, 写成连续形式拆开
11. 导
12. 这里$E = E(x^2)\_{n=m}\cdot I_{nm} $+ $(1 - I_{nm})\cdot E(x_n)E(x_m)$
13. 求期望,使用二阶矩等于$\mu^2+\sigma^2$ 
14. $W = \frac{W+W^T}{2} + \frac{W-W^T}{2}$ 一个对称一个反称.然后带入乘开, 反称项抵消. 对称阵的参数就是上三角或者下三角.
15. 
16. 
17. 
18. 这四道照着题干做
19. $a\sqrt D$, 可以用向量空间中点的距离算(虽然是很直觉的东西)
20. **这个比较麻烦 有个地方没想通 先放着**  
21. $f(x) = \sqrt {ax} - a, x>a$
22. 对$y$, $\arg\min\sum_k L_{kj}p(c_k|x) = \arg\min[1-p(c_j|k)]$, 就是取得$p(c_j|x)$最大的$j$. (mathjax怎么没有argmax argmin的符号, 很迷)
23. 把$p(C_k|x)$用Bayes公式写开, 就得到先验与loss matrix的关系
24. 先求出total loss, 若大于$\lambda$就选择拒绝, 即看$\min_j \sum_k L_{kj}p(C_j| x) <\lambda$就接受
25. 求导有问题的话, 就把$\frac{\partial E}{\partial y(x)}$看作$\frac{\partial E}{y'\partial x}$
26. 
---




