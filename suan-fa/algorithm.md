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

## 4.输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回

首先需要明白的一点是，我们是从根结点出发，不停的找左右子树，来重新构建这个树的。那么解法如下 

1.找到整棵树的根节点。如何找？前序遍历的第一个元素就是根节点

2.找到这个根节点的左右节点。如何找？在中序遍历找到前面找到的根结点的位置，一分为二，出来两个序列

pre \[1,2,4,7,3,5,6,8\]  tin \[4,7,2,1,5,3,8,6\]

前序的第一个元素就是根节点，那么在tin中找到它的位置

\[4,7,2\] \[1\] \[5,3,8,6\]

根结点位于第3（从0开始）

那么在前序中我们就可以根据这个位置得到左子树的长度,右子树的长度

\[1\] 

\[2,4,7\]   ----&gt; pre\[1:i+1\]

 \[3,5,6,8\] ------&gt; pre\[i+1:\]

这两个子树的中序遍历就出来了

\[4,7,2\] -------&gt; tin\[:i\]

\[1\]

\[5,3,6,8\]--------&gt;tin\[i+1:\]

对这两个子树递归处理

每一次递归调用返回的都是的是根结点

```python
class Solution:
    # 返回构造的TreeNode根节点
    def reConstructBinaryTree(self, pre, tin):
        #0.错误处理
        if not pre or not tin:
            return None
        
        #1.构造一棵树，传入根节点 并用root指向这个根节点
        root = TreeNode(pre[0])
        
        #2.找到中序遍历中的根节点index
        i = tin.index(pre[0])
        
        #3.对切割后的左右子树继续切割
        #左子树的中序遍历和前序遍历
        root.left = self.reConstructBinaryTree(pre[1:i+1], tin[0:i])
        #右子树的中序遍历和前序遍历
        root.right = self.reConstructBinaryTree(pre[i+1:], tin[i+1:])
        
        return root
```

## 5.用两个栈来实现一个队列，完成队列的Push和Pop操作

思路

定义两个栈 stackIn , stackOut

入栈操作很简单, append就行

出栈操作分两种:

1.如果stackOut是空的，就把stackIn 里面的元素全部压入stackOut里面，让stackOut执行pop操作

2.如果stackOut不是空的，说明上一次出栈时，stackOut还有数据没有pop完，直接让stackOut pop即可

```python
# -*- coding:utf-8 -*-
class Solution:
    #1.定义两个list
    def __init__(self):
        self.stackIn = []
        self.stackOut = []
    
    def push(self, node):
        #2.入栈操作很简单，直接append
        self.stackIn.append(node)
        
    def pop(self):
        #3.出栈操作分两步
        #3.1 判断stackOut是否为空,如果为空，把In里面的数据都移动到Out里面来
        if self.stackOut == []:
            while self.stackIn != []:
                self.stackOut.append(self.stackIn.pop())
            return self.stackOut.pop()
        #3.2不为空,直接pop
        else:
            return self.stackOut.pop()
```

## 6.把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。 输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。 例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。 NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

思路:既然看到了有序，考虑用二分法，但是这也不是单纯的有序，是进行了部分逆序的数组，所以要考虑一些特殊的情况

```python
# -*- coding:utf-8 -*-
class Solution:
    def minNumberInRotateArray(self, rotateArray):
        # write code here
        #0.异常处理
        if not rotateArray:
            return 0
        #二分法
        #1.找high low mid位
        high = len(rotateArray)-1
        low = 0
        
        while (low <= high):
            mid = int((high + low)/2)
            #2.让mid与high去比较
            #如果mid比high大,说明最小值在右边
            #low=mid+1
            if rotateArray[mid] > rotateArray[high]:
                low = mid + 1
            #如果mid比high小,说明最小值在左边,或者就是自己本身
            elif rotateArray[mid] < rotateArray[high]:
                #是自己本身的情况,那就是mid比high小，也比左边的数小
                if rotateArray[mid] < rotateArray[mid-1]:
                    return rotateArray[mid]
                #不是，更改high的位置
                else:
                    high = mid-1
            else:
                return rotateArray[mid]
```

## 7.大家都知道斐波那契数列，现在要求输入一个整数n，请你输出斐波那契数列的第n项（从0开始，第0项为0，第1项是1）。n&lt;=39

首先明确，什么是斐波那契数列

用文字来说，就是斐波那契数列由0和1开始，之后的斐波那契数就是由之前的两数相加而得出。首几个斐波那契数是：[0](https://zh.wikipedia.org/wiki/0), [1](https://zh.wikipedia.org/wiki/1), [1](https://zh.wikipedia.org/wiki/1), [2](https://zh.wikipedia.org/wiki/2), [3](https://zh.wikipedia.org/wiki/3), [5](https://zh.wikipedia.org/wiki/5), [8](https://zh.wikipedia.org/wiki/8), [13](https://zh.wikipedia.org/wiki/13), [21](https://zh.wikipedia.org/wiki/21), [34](https://zh.wikipedia.org/wiki/34), [55](https://zh.wikipedia.org/wiki/55), [89](https://zh.wikipedia.org/wiki/89), [144](https://zh.wikipedia.org/wiki/144), [233](https://zh.wikipedia.org/wiki/233)

思路1:递归,比较耗时

```python
# -*- coding:utf-8 -*-
class Solution:
    def Fibonacci(self, n):
        #0.异常处理
        if n == 0 or n == 1:
            return n
        
        #1.递归处理
        return self.Fibonacci(n-1) + self.Fibonacci(n-2)
```

思路：循环

```python
class Solution:
    def Fibonacci(self, n):
        # write code here
        #0.异常处理
        if n == 0 or n == 1:
            return n
        
        #1.计算结果是第一个数加上第二个数,循环叠加
        first = 0
        second = 1
        result = 0
        for i in range(2, n+1):
            result = first + second
            first = second
            second = result
        return result
```

## 8.一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法

思路:斐波那契数列变种

```python
# -*- coding:utf-8 -*-
class Solution:
    def jumpFloor(self, n):
        # write code here
        #0.异常处理
        if n == 1 or n == 2:
            return n
        
        #1.计算结果是第一个数加上第二个数,循环叠加
        first = 1
        second = 2
        result = 3
        for i in range(3, n+1):
            result = first + second
            first = second
            second = result
        return result
```

## 9.一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

还是斐波那契的变种

分析:  
       当n = 1 时， 只有一种跳法，即1阶跳：Fib\(1\) = 1;

       当n = 2 时， 有两种跳的方式，一阶跳和二阶跳：Fib\(2\) = Fib\(1\) + Fib\(0\) = 2;

       当n = 3 时，有三种跳的方式，第一次跳出一阶后，后面还有Fib\(3-1\)中跳法； 第一次跳出二阶后，后面还有Fib\(3-2\)中跳法；第一次跳出三阶后，后面还有Fib\(3-3\)中跳法

        Fib\(3\) = Fib\(2\) + Fib\(1\)+Fib\(0\)=4;

       当n = n 时，共有n种跳的方式，第一次跳出一阶后，后面还有Fib\(n-1\)中跳法； 第一次跳出二阶后，后面还有Fib\(n-2\)中跳法..........................第一次跳出n阶后，后面还有 Fib\(n-n\)中跳法.

       Fib\(n\) = Fib\(n-1\)+Fib\(n-2\)+Fib\(n-3\)+..........+Fib\(n-n\)=Fib\(0\)+Fib\(1\)+Fib\(2\)+.......+Fib\(n-1\)

      又因为Fib\(n-1\)=Fib\(0\)+Fib\(1\)+Fib\(2\)+.......+Fib\(n-2\)

      两式相减得：Fib\(n\)-Fib\(n-1\)=Fib\(n-1\)         =====》  Fib\(n\) = 2\*Fib\(n-1\)     n &gt;= 2

```python
# -*- coding:utf-8 -*-
class Solution:
    def jumpFloorII(self, number):
        if number==1 or number==0:
            return number
        return 2*self.jumpFloorII(number-1)
```



## 10.我们可以用2_1的小矩形横着或者竖着去覆盖更大的矩形。请问用n个2_1的小矩形无重叠地覆盖一个2\*n的大矩形，总共有多少种方法？

思路：还是斐波那契....

```python
# -*- coding:utf-8 -*-
class Solution:
    def rectCover(self, number):
        #0.异常处理
        if number == 0 or number == 1 or number == 2:
            return number
        #1.相加
        first = 1
        second = 2
        result = 0
        
        for i in range(3, number+1):
            result = first + second
            first = second
            second = result
            
        return result
```

