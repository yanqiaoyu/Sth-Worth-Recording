---
description: 每天起床第一句，先来一道算法题
---

# Algorithm

## 1.在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

思路1：暴力破解，遍历整个数组得到结果

```python
def solution(target, array):
    #1.异常排除：数组为空
    if array == None:
        return False
    #2.得到每一行的长度
    hangLength = len(array[0])
    #3.得到每一列的长度
    lieCount = len(array)
    #4.通过两个for循环遍历整个二维数组
    for i in range(lieCount):
        for j in range(hangLength):
            if array[i][j] == target:
                return True        
    #5.遍历了整个数组仍然没有的话就要返回False了
    return False    
```

思路2：由于数组顺序递增，选取右上角或者左下角作为pivot（如果逆序的话，就换一个对角线）。这里选取左下角作为pivot。比pivot大，说明比pivot所在的一整列都大。比pivot小，说明比pivot所在的一整行都小

```python
def solution(array, target):
    #1.先找到行的长度，和有多少行
    hangLength = len(array[0])
    hangCount = len(array)
    
    pivotHang = hangCount-1
    pivotLie = 0
    
    #2.选取这个矩阵中左下角的那个元素作为pivot,它是最后一行的第一个元素
    pivotVar = array[pivotHang][pivotLie]
    
    #3.利用一个循环开始查找
    while (if pivotLie <= hangLength) or (if pivotHang > 0):
        #4.先跟pivot进行比较,相等，返回
        if pivot == target:
            return True
        #5.target比pivot大，pivot的列数+1
        elif pivot < target:
            pivotLie += 1
            pivot = array[pivotHang][pivotLie]
        #6.target比pivot小, pivot的行数-1
        elif pivot > target:
            pivotHang -= 1
            pivot = array[pivotHang][pivotLie]
    #7.遍历了仍然没有，则不存在
    return False

```

