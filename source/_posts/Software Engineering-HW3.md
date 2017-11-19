---
title: Software Engineering-HW3
date: 2017-10-05 10:04:08
tags: HW
---

## 项目地址

https://github.com/lishiyuwhu/Homework/tree/master/SE/HW3

## PSP

| PSP2.1                                  | Personal Software Process Stages        | 预估耗时（分钟） | 实际耗时（分钟） |
|-----------------------------------------|-----------------------------------------|------------------|------------------|
| Planning                                | 计划                                    |                  |                  |
| · Estimate                              | · 估计这个任务需要多少时间              |     20             |      15            |
| Development                             | 开发                                    |                  |                  |
| · Analysis                              | · 需求分析 (包括学习新技术)             |        420          |     600             |
| · Design Spec                           | · 生成设计文档                          |         30         | 20                 |
| · Design Review                         | · 设计复审 (和同事审核设计文档)         |            60      |       80           |
| · Coding Standard                       | · 代码规范 (为目前的开发制定合适的规范) |        10          |         10         |
| · Design                                | · 具体设计                              |    30              |   50               |
| · Coding                                | · 具体编码                              |     120             |   180               |
| · Code Review                           | · 代码复审                              |       30           |    20              |
| · Test                                  | · 测试（自我测试，修改代码，提交修改）  |         60         |      40            |
| Reporting                               | 报告                                    |                  |                  |
| · Test Report                           | · 测试报告                              |      20            |   10               |
| · Size Measurement                      | · 计算工作量                            |      10            |           10       |
| · Postmortem & Process Improvement Plan | · 事后总结, 并提出过程改进计划          |       30           |      50            |
| 合计                                    |                                         |    860              |     1085             |


## 解题思路

开始的时候, 首先确定使用python进行开发. 之后就GUI工具进行了比较, 选用了PyQt, 最终决定PyQt5+python3的开发环境. 资料并不多, 开始看了使用一些中文资料, 而后[PyQt5 tutorial](http://zetcode.com/gui/pyqt5/)配合官方文档进行学习. 

计时模块方面, 原意是直接在旁边写一个时钟. 但学习时发现应该使用多线程, 否则会计时不准. 最后采用的方案是调用clock()函数, 每次检查答案时会返回时间.


三个目标:

- 记录对错: 生成一个txt用来存储记录
    
    完成
 - 计时: 主界面旁边再写个计时器模块
    
    ~~多线程~~
    
    调用clock()
 - 多语言: ~~三个语言的txt, 需要使用时候从中调用~~
 
 	使用Qt的多语言工具

## 设计实现过程

首先修改了HW2, 得到一个

```python
def produceOpListMid():  # 返回一个[str(OpList), ANS]  i.e.[' 4/3 + 2/3 ÷ 9/7 - 7/8', [Fraction(211, 216)]]
```
函数作为GUI调用的主体.

实现顺序:

基本功能GUI -> 记录对错 -> 计时功能 -> 多语言切换 

## 代码说明


两个槽函数来实现记录存储. 在运行目录下生成一个file.txt记录答对的题目数和已答题的数目. 

```python
    def newfile(self):
        f = open('file.txt', 'w')
        f.write('0\n0')
        f.close()
        clock()
        self.putstrin()
        self.scoreEdit.setText(
            '正确题目数: 0 \n题目总数: 0 \n分数: 无 \n时间%.f s' % 0)

    def loadfile(self):
        global rightnum, totalnum
        try:
            f = open('file.txt', 'r')
        except:
            f = open('file.txt', 'w')
            f.write('0\n0')

        twonum = f.readlines()
        f.close()
        rightnum = int(twonum[0])
        totalnum = int(twonum[1])
        if totalnum == 0:
            self.scoreEdit.setText(
                '正确题目数: 0 \n题目总数: 0 \n分数: 无 \n时间%.f s' % 0)
        else:
            self.outputtobox()
```


## 测试运行

## 合作情况

## 项目小结

## 中期汇报