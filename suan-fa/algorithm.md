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
# -*- coding:utf-8 -*-
class Solution:
    # array 二维列表
    def Find(self, target, array):
        #1.异常处理
        if array == None:
            return False
        
        #2.选取左下角为pivot
        #左下角的坐标为 array[len(array)-1][0]
        hang = len(array)-1
        lie = 0
        #考虑[[]]的情况
        if hang <= 0:
            return False
        #3.开始比较,外围是一个循环
        while True:
            #如果pivot为target
            if array[hang][lie] == target:
                return True
            #如果pivot比target大，去掉当前这一行
            elif array[hang][lie] > target:
                hang -= 1
                #如果减掉1后已经越界,直接false
                if hang < 0 :
                    return False
            #如果pivot比target小，去掉当前这一列
            elif array[hang][lie] < target:
                lie += 1
                #如果加1越界，直接false
                if lie > len(array[0])-1:
                    return False
```

## 2.请实现一个函数，将一个字符串中的每个空格替换成“%20”。例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy。

思路1 遍历替换

```python
class Solution:
    def replaceSpace(self, s):
        # write code here
        s = list(s)
        count=len(s)
        for i in range(0,count):
            if s[i]==' ':
                s[i]='%20'
        return ''.join(s)
        
        ''' 运行时间：26ms 占用内存：5860k'''
```

思路2 空间换时间

```python
class Solution:
    # s 源字符串
    def replaceSpace(self, s):
        # write code here
        newList = []
        oldList = list(s)
        for i in range(len(s)):
            if oldList[i] == ' ':
                newList.append('%20')
            else:
                newList.append(oldList[i])
                
        return ''.join(newList)
        
        '''运行时间：26ms 占用内存：5860k'''
```

## 3.输入一个链表，按链表从尾到头的顺序返回一个ArrayList

思路: reverse\(\), insert\(\), return \[::-1\]

 没啥好说的，也可以用reverse\(\)

```python
class Solution:
    # 返回从尾部到头部的列表值序列，例如[1,2,3]
    def printListFromTailToHead(self, listNode):
        s=[]
        while listNode:
            s.append(listNode.val)
            listNode = listNode.next
        return s[::-1]
        
class Solution:
    # 返回从尾部到头部的列表值序列，例如[1,2,3]
    def printListFromTailToHead(self, listNode):
        
```

```python
class Solution:
    # 返回从尾部到头部的列表值序列，例如[1,2,3]
    def printListFromTailToHead(self, listNode):
        head = listNode
        #利用insert()函数
        resultList = []
        #利用一个循环读取链表
        while head:
            resultList.insert(0, head.val)
            head = head.next        
        return resultList
```

