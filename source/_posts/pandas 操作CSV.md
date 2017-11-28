---
title: pandas 操作CSV
date: 2017-11-25  14:45:31
tags: ML
---

```
import pandas as pd  
train_data = pd.read_csv("test.csv")  
```

read_csv, 返回数据类型 DataFrame

## 以某列为主键merge两个CSV

```
pd.merge(data_employee_ri,data_employee_ri,on='name')
```



## 抽取数据


```python
data.loc[1] //返回单列数据
data.loc[1:3] //返回切片列数据，相当于 data.loc[[1,2,3]]
data.loc[:,['ID’]]
data.loc[:4,['ID','YELP’]] //返回指定行的指定类，从0行到4行，包括第4行

data.iloc[:,:] //返回所有数据
data.iloc[:2,1:3] //返回特定行特定列的数据

data[‘ID’] //返回列
data[2:5] //返回行
data[3:6][:2]
data[‘ID’][3:6]
data[3:6][‘ID’]
```

1.选择特定列和行的数据

 a['x'] 那么将会返回columns为x的列，注意这种方式一次只能返回一个列。a.x与a['x']意思一样。

 取行数据，通过切片[]来选择, 如：a[0:3] 则会返回前三行的数据。

 ## 就是取列, 直接a['A'], 取行要按切片方式取



2.loc是通过标签来选择数据

 a.loc['one']则会默认表示选取行为'one'的行；

 a.loc[:,['a','b'] ] 表示选取所有的行以及columns为a,b的列；

 a.loc[['one','two'],['a','b']] 表示选取'one'和'two'这两行以及columns为a,b的列；

 a.loc['one','a']与a.loc[['one'],['a']]作用是一样的，不过前者只显示对应的值，而后者会显示对应的行和列标签。


3.iloc则是直接通过位置来选择数据

 这与通过标签选择类似
 a.iloc[1:2,1:2] 则会显示第一行第一列的数据;(切片后面的值取不到)

 a.iloc[1:2] 即后面表示列的值没有时，默认选取行位置为1的数据;

 a.iloc[[0,2],[1,2]] 即可以自由选取行位置，和列位置对应的数据。

 data.iloc[-1]   #选取DataFrame最后一行，返回的是Series
 data.iloc[-1:]   #选取DataFrame最后一行，返回的是DataFrame


4.使用条件来选择

 使用单独的列来选择数据
 a[a.c>0] 表示选择c列中大于0的数据

 使用where来选择数据
 a[a>0] 表直接选择a中所有大于0的数据

 使用isin()选出特定列中包含特定值的行
 a1=a.copy()
 a1[a1['one'].isin(['2','3'])] 表显示满足条件：列one中的值包含'2','3'的所有行。


## 操作

统计某一列x中各个值出现的次数：a['x'].value_counts()


### 出现次数最多的元

使用np.bincount()和np.argmax()函数来实现。
np.bincount()可以把数组中出现的每个数字，当做index，数字出现的次数当做value来表示。
np.argmax()可以返回数组中最大值的index。
所以经过变换，可以得到出现次数的最大值。
```
np.argmax(np.bincount(line))
```

## 去重

DataFrame的duplicated方法返回一个布尔型Series,表示各行是否重复行。

而 drop_duplicates方法，它用于返回一个移除了重复行的DataFrame

这两个方法会判断全部列，你也可以指定部分列进行重复项判段。
例如，希望对名字为k2的列进行去重，
data.drop_duplicates(['k2'])