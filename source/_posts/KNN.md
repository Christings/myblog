---
title: KNN
date: 2018-09-10 14:26:36
tags: knn
category:
		- 机器学习和深度学习
		- 《机器学习实战》
---

```python
#coding:utf-8

import numpy as np
import operator
```


```python
def createDataSet():
    group=np.array([[1.0,1.1],[1.0,1.0],[0,0],[0,0.1]])
    labels=['A','A','B','B']
    return group,labels
```


```python
group,labels=createDataSet()
```


```python
group,labels
```




    (array([[1. , 1.1],
            [1. , 1. ],
            [0. , 0. ],
            [0. , 0.1]]), ['A', 'A', 'B', 'B'])




```python
def classify0(inX,dataSet,labels,k):
    dataSetSize=dataSet.shape[0] # dataSet的行数
    print("dataSetSize:",dataSetSize)
    diffMat=np.tile(inX,(dataSetSize,1))-dataSet # 构造一个和dataSet一样的矩阵，再和dataSet相减
    print("diffMat:",diffMat)
    sqDiffMat=diffMat**2 # 矩阵的平方
    print("sqDiffMat:",sqDiffMat)
    sqDistances=sqDiffMat.sum(axis=1) # 按行相加
    print("sqDistances:",sqDistances)
    distances=sqDistances**0.5 # 开方，求距离
    print("distances:",distances)
    sortedDistIndicies=distances.argsort() # 对距离排序
    print("sortedDistIndicies:",sortedDistIndicies)
    classCount={}
    for i in range(k):
        voteIlabel=labels[sortedDistIndicies[i]]
        print("voteIlable:",voteIlabel)
        classCount[voteIlabel]=classCount.get(voteIlabel,0)+1
        print("classCount:",classCount)
    sortedClassCount=sorted(classCount.items(),key=operator.itemgetter(1),reverse=True)
    print(sortedClassCount)
    return sortedClassCount[0][0]
classify0([1.0,1.2],group,labels,3)
```

    dataSetSize: 4
    diffMat: [[0.  0.1]
     [0.  0.2]
     [1.  1.2]
     [1.  1.1]]
    sqDiffMat: [[0.   0.01]
     [0.   0.04]
     [1.   1.44]
     [1.   1.21]]
    sqDistances: [0.01 0.04 2.44 2.21]
    distances: [0.1        0.2        1.56204994 1.48660687]
    sortedDistIndicies: [0 1 3 2]
    voteIlable: A
    classCount: {'A': 1}
    voteIlable: A
    classCount: {'A': 2}
    voteIlable: B
    classCount: {'A': 2, 'B': 1}
    [('A', 2), ('B', 1)]





    'A'




```python
# 将文本记录转换为numpy矩阵
def file2matrix(filename):
    fr=open(filename)
    
    numberOfLines=len(fr.readlines())
    returnMat=np.zeros((numberOfLines,3)) # 创建一个和文本一样形式的0矩阵
    classLabelVector=[]
    fr=open(filename)
    
    index=0
    for line in fr.readlines():
        line=line.strip()
        listFromLine=line.split('\t')
        returnMat[index,:]=listFromLine[0:3]
        classLabelVector.append(int(listFromLine[-1]))
        index+=1
    return returnMat,classLabelVector
```


```python
datingDataMat,datingLabels=file2matrix('datingTestSet2.txt')
datingDataMat,datingLabels[0:20]
```




    (array([[4.0920000e+04, 8.3269760e+00, 9.5395200e-01],
            [1.4488000e+04, 7.1534690e+00, 1.6739040e+00],
            [2.6052000e+04, 1.4418710e+00, 8.0512400e-01],
            ...,
            [2.6575000e+04, 1.0650102e+01, 8.6662700e-01],
            [4.8111000e+04, 9.1345280e+00, 7.2804500e-01],
            [4.3757000e+04, 7.8826010e+00, 1.3324460e+00]]),
     [3, 2, 1, 1, 1, 1, 3, 3, 1, 3, 1, 1, 2, 1, 1, 1, 1, 1, 2, 3])




```python
# 分析数据，使用Matplotlib创建散点图
import matplotlib
import matplotlib.pyplot as plt
import numpy as np
# mpl.rcParams['font.sans-serif'] = ['SimHei']  
# mpl.rcParams['axes.unicode_minus'] = False  
fig=plt.figure()
ax=fig.add_subplot(111)

datingLabels = np.array(datingLabels)

idx_1=np.where(datingLabels==1)
p1=ax.scatter(datingDataMat[idx_1,0],datingDataMat[idx_1,1],color='y',label='1',s=10)
idx_2=np.where(datingLabels==2)
p2=ax.scatter(datingDataMat[idx_2,0],datingDataMat[idx_2,1],color='g',label='1',s=20)
idx_3=np.where(datingLabels==3)
p3=ax.scatter(datingDataMat[idx_3,0],datingDataMat[idx_3,1],color='b',label='1',s=30)

# ax.scatter(datingDataMat[:,1],datingDataMat[:,2],15.0*np.array(datingLabels),15.0*np.array(datingLabels),c='yellow')

plt.xlabel(u'每年获取的飞行常客里程数')
plt.ylabel(u'玩视频游戏所耗时间百分比')
ax.legend((p1,p2,p3), (u'不喜欢', u'魅力一般', u'极具魅力'), loc=2)
# ax.rc('font',family=['SeiHei'])
#ax.legend((p1,p2,p3), (u'不喜欢', u'魅力一般', u'极具魅力'), loc=2, prop=zhfont)
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.show()
```


![png](Untitled_files/Untitled_7_0.png)



```python
# 归一化特征值
def autoNorm(dataSet):
    minVals=dataSet.min(0) # 3列数据中的3个最小值
    maxVals=dataSet.max(0) # 3列数据中的3个最大值
    print(minVals)

    ranges=maxVals-minVals
    normDateSet=np.zeros(np.shape(dataSet))
    print(normDateSet)
    m=dataSet.shape[0] # 行数1000
    normDateSet=dataSet-np.tile(minVals,(m,1)) #dataSet减去同样格式的最小值组成的矩阵
    print(normDateSet)
    normDateSet=normDateSet/np.tile(ranges,(m,1))
    return normDateSet,ranges,minVals
```

normMat,ranges,minVals=autoNorm(datingDataMat)


```python
# 分类器针对约会网站的测试代码
def datingClassTest():
    hoRatio=0.5
    datingDataMat,datingLabels=file2matrix('datingTestSet2.txt')
    normMat,ranges,minVals=autoNorm(datingDataMat)
    m=normMat.shape[0]
    numTestVecs=int(m*hoRatio)
    errorCount=0.0
    for i in range(numTestVecs):
        classifierResult=classify0(normDateSet)
```
