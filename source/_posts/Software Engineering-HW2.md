---
title: Software Engineering-HW2
date: 2017-09-21 10:35:47
tags: HW
---
# 题目描述

从《构建之法》第一章的 “程序” 例子出发，完成一个能自动生成小学四则运算题目的命令行 “软件”，满足以下需求：

1.参与运算的操作数(operands)除了100以内的整数以外，还要支持真分数的四则运算，例如：1/6 + 1/8 = 7/24。操作数必须随机生成。

2.运算符(operators)为 +, −, ×, ÷ （如运算符个数固定，则不得小于3）运算符的种类和顺序必须随机生成。

3.要求能处理用户的输入，并判断对错，打分统计正确率。

4.使用 -n 参数控制生成题目的个数，例如执行下面命令将生成5个题目

 - (以C/C++/C#为例) calgen.exe -n 5
 - (以python为例) python3 calgen.py -n 5

附加功能（算附加分）

 - 支持带括号的多元复合运算
 - 运算符个数随机生成（考虑小学生运算复杂度，范围在1~10）

# 项目地址

https://github.com/lishiyuwhu/Homework/tree/master/SE/HW2

---

# PSP

| PSP2.1                                  | Personal Software Process Stages        | 预估耗时（分钟） | 实际耗时（分钟） |
|-----------------------------------------|-----------------------------------------|------------------|------------------|
| Planning                                | 计划                                    |                  |                  |
| · Estimate                              | · 估计这个任务需要多少时间              |    5              |    10             |
| Development                             | 开发                                    |                  |                  |
| · Analysis                              | · 需求分析 (包括学习新技术)             |       20           |      15           |
| · Design Spec                           | · 生成设计文档                          |       20           |       0           |
| · Design Review                         | · 设计复审 (和同事审核设计文档)         | 0                 |  0                |
| · Coding Standard                       | · 代码规范 (为目前的开发制定合适的规范) | 0                 |      0            |
| · Design                                | · 具体设计                              |          20        |           15       |
| · Coding                                | · 具体编码                              |           180       |       190           |
| · Code Review                           | · 代码复审                              |           20       |          10       |
| · Test                                  | · 测试（自我测试，修改代码，提交修改）  |    60              |   40+30               |
| Reporting                               | 报告                                    |                  |                  |
| · Test Report                           | · 测试报告                              |           10      |           5      |
| · Size Measurement                      | · 计算工作量                            |           10       |           1       |
| · Postmortem & Process Improvement Plan | · 事后总结, 并提出过程改进计划          |           30       |      30            |
| 合计                                    |                                         |            340      |          316+30       |

~~合着干完了才发现还有设计文档这事~~

---

# 解题思路

先找找python有没有分数类型, 没的话找个包, 实在没有就自己写个~~虽然感觉不可能没有~~. 

具体算符随机和计算就用[密码学作业](https://github.com/lishiyuwhu/Homework/tree/master/Cryptography/HW2) 上刚写的压栈计算器, 大致改改. 

再写个前缀表达式转中缀带括号形式的输出函数. 

python获取命令行参数的函数也要找一下.

注意除0.

还有:

>参与运算的操作数(operands)除了100以内的整数以外

看例子的数据范围, 真的不是0~10吗? 100以内真的好难做.

---

# 设计实现过程

+-*/四个算符属于一个oper类, 
```python
class oper(object):
	f = 0
	n = 0
	name = ''
	def __init__(self, func, num, name, output):
		self.f = func#计算入口
		self.n = num#算符目数
		self.name = name#字母名字
		self.output = output#输出用的符号名字
	def __str__(self):
		return self.output
```

计算用的cal()通过修改得到

一个输出函数, 把OpListwithNumList转换为中序并输出.

match()判断表达式是否成立, 能够计算

# 代码说明
```python
def cal(OpList, NumList): #计算栈函数
	s = []  # 清空计算用的stack
	s = copy.copy(NumList[::-1])
	flag = 0  # 值若为1, 说明此OpList错误, 就舍弃
	for op in OpList[::-1]:  # 前缀, 所以倒过来压栈
		if (op.n > len(s)):  # 栈中, 操作符需要的数不足
			flag = 1
			break
		if (op.n == 0):
			s.append(op.f())
		elif (op.n == 1):
			temp = s.pop()  # 栈顶退栈写入temp
			s.append(op.f(temp))  # 操作后压栈
		elif (op.n == 2):
			temp1 = s.pop()
			temp2 = s.pop()  # 退栈2个
			if (op in [OpDiv]) and temp2==0 :#处理除0模0情形
				flag = 1
				break
			else:
				s.append(op.f(temp1, temp2))  # 计算后压栈
		elif (op.n == 3):
			tempx = s.pop()
			tempy = s.pop()
			tempz = s.pop()
			if op==OpPD and tempz==0:
				flag = 1
				break
			s.append(op.f(tempx, tempy, tempz))  # 计算后压栈
		# 这部分其实应该用dict来写, 这么多的if一点也不pythonic, 但真的懒得写了
	if (len(s) != 1):  # 栈里面的数字没用完
		flag = 1
	if flag==1:
		return 'False'
	else:
		return s[0]
```

(后记: 现在想想其实这里也不算画蛇添足写的'False', 当时我还是有着一颗判断前缀式合法的心的)

## 测试运行

![](http://otivusbsc.bkt.clouddn.com/ee21091a-a9bd-416a-9ecf-4c7e3181d059)

## 项目小结
有个问题直到最后写文书才看出来: 预计的match()没用上. 那么问题来了, 为什么没用上呢? 因为当时直接想的是既然+-/*都是双目运算符, 直接数字个数比算符个数多1就好了. 那这个错误想法为啥直到最后最后也没出错呢? 错在使用的cal()上面.

之前说cal()是从密码学作业改过来的, 本体是
```python
def cal(OpList, pair):# 计算栈函数, 输入一列操作符OpList, 明文密文pair. 返回flag=1说明错误,flag=1正确
```
这个pair类似于[1,5]明文1密文5, 这样cal()运算的数字只有一个, 形如OpList+OneNum. 而又想当然的把OneNum改成NumList, 导致:
```python
 +*-/59874 #可以生成的都是形如OpList+NumList
 +/*23-21*3-41 #运算符嵌在数字中形式的无法生成
```

还是挺可怕的, 一直都没发现. 

不过, 就结果上说, 这也凑合算是

>支持带括号的多元复合运算

了吧😀.


---

update: 
2017/9/24 23:20:10
忘了加上0, 然后不要忘了处理frac{}{0}的情形, 得闲再说

2017/9/25 9:45:14
处理了一票带0情形 