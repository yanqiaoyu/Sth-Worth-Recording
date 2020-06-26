---
description: 每天起床第一句，先来一道算法题
---

# 剑指Offer

## Done 1.在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

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

**思路2：由于数组顺序递增，选取右上角或者左下角作为pivot（如果逆序的话，就换一个对角线）。这里选取左下角作为pivot。比pivot大，说明比pivot所在的一整列都大。比pivot小，说明比pivot所在的一整行都小**

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

## Done 2.请实现一个函数，将一个字符串中的每个空格替换成“%20”。例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy。

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

## Done 3.输入一个链表，按链表从尾到头的顺序返回一个ArrayList

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

## Note:4.输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回

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

## Done 5.用两个栈来实现一个队列，完成队列的Push和Pop操作

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

## Important 6.把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。 输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。 例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。 NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

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

## Important 7.大家都知道斐波那契数列，现在要求输入一个整数n，请你输出斐波那契数列的第n项（从0开始，第0项为0，第1项是1）。n&lt;=39

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

思路2：计划递归

```python
class Solution:
    def __init__(self):
        self.cache = {}
    def Fibonacci(self, n):
        #计划递归
        if n == 0 or n == 1:
            return n
                
        if n in self.cache:
            return self.cache[n]
        else:
            self.cache[n] = self.Fibonacci(n-1) + self.Fibonacci(n-2)
        return self.cache[n]
```

思路3：动态规划

这里有个坑：a,  b = b, a+b 不能写成 a = b b = a+b

```python
# -*- coding:utf-8 -*-
class Solution:
    def Fibonacci(self, n):
        # write code here
        a=0
        b=1
        
        for _ in range(n):
            a, b = b, a+b 
        return a
```

## Same\(7\) 8.一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法

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

## Same\(7\) 9.一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

还是斐波那契的变种

**重要结论： Fib\(n\) = 2\*Fib\(n-1\)     n &gt;= 2**

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

## 11.输入一个整数，输出该数二进制表示中1的个数。其中负数用补码表示。

思路：如果一个整数不为0，那么这个整数至少有一位是1。如果我们把这个整数减1，那么原来处在整数最右边的1就会变为0，原来在1后面的所有的0都会变成1\(如果最右边的1后面还有0的话\)。其余所有位将不会受到影响。 把原来的整数和减去1之后的结果做与运算，从原来整数最右边一个1那一位开始所有位都会变成0。

举例来说，6的二进制是 110 ，6-1=5的二进制是 101，6&5=100， 如此操作之后6中原来的110变为100，循环计数统计1的个数，直至n变为0为止。

```python
# -*- coding:utf-8 -*-
class Solution:
    def NumberOf1(self, n):
        count = 0
        while n&0xffffffff != 0:
            count += 1
            n = n & (n-1)
        return count
```

## 12.给定一个double类型的浮点数base和int类型的整数exponent。求base的exponent次方。保证base和exponent不同时为0

思路: C++里面可能要考虑精度的问题，但是Python里面没有这样的问题，直接求乘积

```python
# -*- coding:utf-8 -*-
class Solution:
    def Power(self, base, exponent):
        #0.异常处理
        #base 为0，数学上不具有意义
        #exp 为0，=1
        if base == 0:
            return False
        if exponent == 0:
            return 1
        
        result = 1
        #注意，这里调用了abs()函数来取绝对值
        for i in range(1,abs(exponent)+1):
            result = result * base
        #1.如果exp是正值
        if exponent > 0:
            return result
        elif exponent < 0:
            return (1/result)
```

## 13.输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分，并保证奇数和奇数，偶数和偶数之间的相对位置不变。

思路：空间换时间

```python
# -*- coding:utf-8 -*-
class Solution:
    def reOrderArray(self, array):
        #0.异常处理
        if len(array) == 0:
            return array
        
        #1.空间换时间 定义两个奇偶数组分别存放最后拼接
        odd = []
        even = []
        result = []
        
        #2.遍历一次 取出数字
        for i in range(len(array)):
            if array[i] % 2 == 1:
                odd.append(array[i])
            else:
                even.append(array[i])
                
        result = odd + even
        return result
```

## 14.输入一个链表，输出该链表中倒数第k个结点。

思路：Python 设置两个指针，p1，p2，先让p2走k-1步，然后再一起走，直到p2为最后一个 时，p1即为倒数第k个节点

举个例子:

\[1,2,3,4,5,6,7,8,9,10\]

找出倒数第2个

先让p1 走 2-1步

然后p1 p2 一起走

p2走到最后

p1就走到了倒数第2个

```python
# -*- coding:utf-8 -*-
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def FindKthToTail(self, head, k):
        # write code here
        #异常处理
        if head == None or k <= 0:
            return None
        
        #1.定义两个指针p1 p2都指向链表的头部
        p1= head 
        p2= head
        
        #2.p1先走k-1步
        for i in range(1,k):
            if p1.next == None:
                return None
            else:
                p1 = p1.next
        
        #3.p1 p2一起走
        while p1.next != None:
            p1 = p1.next
            p2 = p2.next
            
        return p2
```

## 15.输入一个链表，反转链表后，输出新链表的表头。

思路：3个指针

![&#x660E;&#x767D;3&#x4E2A;&#x6307;&#x9488;&#x7684;&#x8D4B;&#x503C;&#x987A;&#x5E8F;](../.gitbook/assets/wei-ming-ming-wen-jian-3.jpg)

```python
class Solution:
    # 返回ListNode
    def ReverseList(self, pHead):
        #0.异常处理
        #pHead = None 或者pHead只有一个
        if pHead ==  None or pHead.next == None:
            return pHead
        
        #1.定义3个指针
        pre = None
        Cur = pHead
        Tmp = None
        
        #2.循环里面不停逆序
        while Cur != None:
            Tmp = Cur.next
            Cur.next = pre
            pre = Cur
            Cur = Tmp
        return pre
```

## 16.输入两个单调递增的链表，输出两个链表合成后的链表，当然我们需要合成后的链表满足单调不减规则。

思路：没啥好说的，遍历，赋值

```python
# -*- coding:utf-8 -*-
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None
class Solution:
    # 返回合并后列表
    def Merge(self, pHead1, pHead2):
        # write code here
        #0.异常处理
        if pHead1 == None:
            return pHead2
        elif pHead2 == None:
            return pHead1
        elif pHead2 == None and pHead1 == None:
            return None
        
        #1.定义一个新的链表，用来存放合成的数据
        pResult = ListNode(0)
        #再定义一个指针，用来移动赋值
        p = pResult
            
        #2.循环处理后面的
        while pHead1 != None and pHead2 != None:
            #如果p2大于等于了p1
            if pHead1.val <= pHead2.val:
                p.next = pHead1
                pHead1 = pHead1.next
            #如果p1大于了p2
            elif pHead1.val > pHead2.val:
                p.next = pHead2
                pHead2 = pHead2.next
            
            #每次赋值完成，p都要后移一位
            p = p.next
        #3.循环跳出了,说明有一个链表到头，后续的链表直接
        if pHead2 != None:
            p.next = pHead2
        else:
            p.next = pHead1
            
        #4.这个链表初始化的时候，给了一个0，但是实际是不需要的
        return pResult.next
```

## 17.输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）

**当Tree1和Tree2都不为零的时候，才进行比较。否则直接返回false**

**如果找到了对应Tree2的根节点的点，**

**以这个根节点为为起点判断是否包含Tree2**

**如果找不到，那么就再去root的左孩子当作起点，去判断时候包含Tree2**

**如果还找不到，那么就再去root的右孩子当作起点，去判断时候包含Tree2**

```python
# -*- coding:utf-8 -*-
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
class Solution:
    def HasSubtree(self, pRoot1, pRoot2):
        #0.异常处理
        if pRoot1 == None or pRoot2 == None:
            return False
        
        #如果p2的根与p1的根相等了，则可能是子树了，开始判断
        #如果第一次的根没找着，继续找左边的根，或者右边的根
        return self.isSubTree(pRoot1, pRoot2) or self.HasSubtree(pRoot1.left,pRoot2) or self.HasSubtree(pRoot1.right, pRoot2)
            
        
    def isSubTree(self, pRoot1, pRoot2):
        #这是一个递归函数，如果递归到最后，pRoot2已经遍历完还没有返回False，说明是子树
        if pRoot2 == None:
            return True
        #！！！如果递归到最后是pRoot1先递归完，说明也不是子树
        if pRoot1 == None:
            return False
        #如果在某一次递归发现了节点不一致,返回false
        if pRoot1.val != pRoot2.val:
            return False
        #进行递归
        return self.isSubTree(pRoot1.left, pRoot2.left) and self.isSubTree(pRoot1.right, pRoot2.right)
        
```

**注意，在HasSubTree里面遇到了一个坑，一开始是这样写的，一直无法通过**

```python
        #如果p2的根与p1的根相等了，则可能是子树了，开始判断
        if pRoot1.val == pRoot2.val
            return self.isSubTree(pRoot1, pRoot2)
        #如果第一次的根没找着，继续找左边的根，或者右边的根
        return  or self.HasSubtree(pRoot1.left,pRoot2) or self.HasSubtree(pRoot1.right, pRoot2)

```

这样写是有问题的，因为即使第一个根一致，一旦后面不一致，就会直接返回False了，而不会接着往下判断其他的根节点，因此如果想要这样写，要用个flag记录第一个根的结果，看看需不需要接着往下判断

## 18.操作给定的二叉树，将其变换为源二叉树的镜像

思路:递归交换就wan sir 了嗷凑弟弟

```python
# -*- coding:utf-8 -*-
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
class Solution:
    # 返回镜像树的根节点
    def Mirror(self, root):
        #0.异常处理
        if root == None:
            return None
        
        #1.如果已经走到了叶子节点
        if root.left == None and root.right == None:
            return root
        #2.不是叶子节点，继续递归交换
        else:
            root.left , root.right = root.right, root.left
            self.Mirror(root.left)
            self.Mirror(root.right)
```

## 19.输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下4 X 4矩阵： 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.

思路：我快吐了，做好异常与特殊值的处理：只有一行？只有一列？只有一个？

以及非矩阵可能重复读取的问题line 52

```python
# -*- coding:utf-8 -*-
class Solution:
    # matrix类型为二维列表，需要返回列表
    def printMatrix(self, matrix):
        #0.异常处理
        if not matrix:
            return False

        if len(matrix[0]) == 1 and len(matrix) != 1:
            result = []
            for i in range(len(matrix)):
                result.append(matrix[i][0])
            return result
        
        if len(matrix) == 1:
            result = []
            for i in range(len(matrix[0])):
                result.append(matrix[0][i])
            return result


        
        #1.定义左上角，右下角两个位置
        '''
        1 2 3 .. 
        4 5 6 ..
        . .
        . .      [y][x]
        '''
        #右下角坐标
        x = len(matrix[0])-1
        y = len(matrix)-1

        #初始坐标
        x0 = 0
        y0 = 0
        
        result = []
        
        #2.循环遍历
        #循环跳出条件,坐标溢出了
        while y0 <= y and x0 <= x:
            #上
            for i in range(x0,x+1):
                result.append(matrix[y0][i])

            #右
            #从y0+1行开始从上往下
            for i in range(y0+1,y+1):
                result.append(matrix[i][x])

            if y0 != y:
                #下
                #从y行开始从右边倒数第二个 从右往左
                for i in range(x-x0):
                    result.append(matrix[y][x-1-i])

                #左    
                #从y-1行开始，从下往上,
                for i in range(y-1-y0):
                    result.append(matrix[y-1-i][x0])

            #坐标缩圈
            x0 += 1
            y0 += 1
            x -= 1
            y -= 1
        print(result)
        return result
```

## 20.定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））。 注意：保证测试中不会当栈为空的时候，对栈调用pop\(\)或者min\(\)或者top\(\)方法。

思路：多定义一个stackMin，用来存放最小值，当然，在压入和弹出的时候，都要判断是不是最小值

```python
class Solution:
    def __init__(self):
        self.stack = []
        self.min_stack = []
    def push(self, node):
        self.stack.append(node)
        #压入新node的时候同时要判断应不应该压入stackMin
        if not self.min_stack or node <= self.min_stack[-1]:
            self.min_stack.append(node)
    def pop(self):
        #同理，弹出node的时候也要判断是不是最小值
        if self.stack[-1] == self.min_stack[-1]:
            self.min_stack.pop()
        self.stack.pop()
    def top(self):
        return self.stack[-1]
    def min(self):
        return self.min_stack[-1]
```

## 21.从上往下打印出二叉树的每个节点，同层节点从左至右打印。

思路：用两个list，一个用来装节点，一个用来装节点的value。对于装节点的这个list，我们每次弹出最前面的节点，并依次将左节点和右节点加在后面。不管怎么样，这个装节点的list都能保证，将节点从上到下，同层节点从左到右进行添加、弹出。

```python
# -*- coding:utf-8 -*-
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
class Solution:
    # 返回从上到下每个节点值列表，例：[1,2,3]
    def PrintFromTopToBottom(self, root):
        # write code here
        #0.异常处理
        if not root:
            return []
        
        #1.定义两个队列,一个存放结果,一个存放每层节点
        result = []
        node = []
        node.append(root)
        
        #2.循环处理
        while node != []:
            tmpNode = node.pop(0)
            result.append(tmpNode.val)
            
            #看这个节点有没有左右节点
            if tmpNode.left:
                node.append(tmpNode.left)
                
            if tmpNode.right:
                 node.append(tmpNode.right)
        
        return result
```

## 22.输入一个非空整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同。

思路:

首先，什么是二叉搜索树？左边的都比根节点小，右边的都比根节点大

其次，后序遍历有什么特点？左-》右-》根

```python
# -*- coding:utf-8 -*-
class Solution:
    def VerifySquenceOfBST(self, sequence):
        #0.异常处理
        if len(sequence) == 1:
            return True
        
        if not sequence:
            return False
        
        #1.找到最后一个元素，它是根节点
        root = sequence[-1]
        Index = 0
        #2.从头开始，找可能的左右子树的分界index
        for i in range(len(sequence)):
            if sequence[i] >= root:
                Index = i
                break
        
        leftSeq = sequence[0:Index]
        rightSeq = sequence[Index:-1]
        
        #3.理论上来说，右子树里面的值，都比根结点大
        for i in range(len(rightSeq)):
            if rightSeq[i] < root:
                return False
            
        #4.递归处理
        #return self.VerifySquenceOfBST(leftSeq) and self.VerifySquenceOfBST(rightSeq)
        leftFlag = True
        rightFlag = True
        #4.不能直接递归，先要判断左右子树为不为空
        if len(leftSeq) > 0:
            leftFlag = self.VerifySquenceOfBST(leftSeq)

        if len(rightSeq) > 0:
            rightFlag = self.VerifySquenceOfBST(rightSeq)

        return leftFlag and rightFlag
    
```

## 23.输入一颗二叉树的根节点和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。

思路:注意题目中关于路径的定义，必须是从根结点到叶结点，也就是说这其实是树的深度优先遍历DFS（前面那道题是广度优先遍历），那么答案基本已经出来了，在深度优先的同时，进行结点值的相加，判断当前路径是否符合期待

注意，可能在递归调用里面传的是path + \[xxx.val\],这可以保证如果最后的结果不是我们要的，可以回退到根结点的位置，而不需要删除已经压入的结点

```python
class Solution:
    # 返回二维列表，内部每个列表表示找到的路径
    def FindPath(self, root, expectNumber):
        # write code here
        #定义用来存放最终结果的二维列表
        result = []
        #0.异常处理
        if not root:
            return result
        
        #定义用来存放单个结果的数组
        path = [root.val]
        
        #1.开始进行深度优先的遍历
        self.DFS(root, expectNumber, path, result)
        return result
        
    def DFS(self, root, expectNumber, path, result):
        #如果左子树，右子树都不存在，且路径之和等于expectNumber
        #那就把当前路径加入总结果
        if not root.left and not root.right and sum(path) == expectNumber:
            result.append(path)
        #如果存在左子树,继续递归，因为是深度优先
        if root.left:
            self.DFS(root.left, expectNumber, path+[root.left.val], result)
        if root.right:
            self.DFS(root.right, expectNumber, path+[root.right.val], result)
```

## 24.输入一个复杂链表（每个节点中有节点值，以及两个指针，一个指向下一个节点，另一个特殊指针random指向一个随机节点），请对此链表进行深拷贝，并返回拷贝后的头结点。（注意，输出结果中请不要返回参数中的节点引用，否则判题程序会直接返回空）

```python
class Solution:
    # 返回 RandomListNode
    def Clone(self, pHead):
        # write code here
        #0.异常处理
        if not pHead:
            return pHead
        
        #1.第一步，复制每个旧结点，并把它链接在旧结点之后
        # 例如 A->B->C 变为 A->A_new->B->B_new->C->C_new
        
        #定义一个tmp指向与pHead一致的位置
        tmp = pHead
        #这个循环显然需要执行到tmp为空的时候
        while tmp:
            #既然是深拷贝，显然需要new一个新的出来
            #初始化传的值，就是需要拷贝的结点的label
            newNode = RandomListNode(tmp.label)
            #想象一下，当前的链表是这样A -----> B
            #                        A.new
            #显然，要建立成 A ---> A.new ---> B 这样的链表，先要让A.new链接B，不然会找不到B
            #让 A.new指向B, B是什么？ B是A.next
            newNode.next = tmp.next
            #此时的链表是  A ---> B
            #                  |
            #               A.new 
            #再让A指向A.new
            tmp.next = newNode
            #最后让tmp指向B，开始新的复制
            tmp = newNode.next
        #循环完成后，tmp到了链表尾部的None位置
        #并且链表已成 A ---> A.new ---> B ---> B.new ...
        
        #再把tmp拉回来，进行第二步
        tmp = pHead
        
        #2.第二步,复制random指针
        while tmp:
            #我们假设加了random的指针是这样的
            #  ------------------
            # |                 |
            # A ---> A.new ---> B ---> B.new
            #那么 A.new的random要指向谁呢？
            #应该要指向，被复制结点的random指针所指向结点的下一个结点
            #             (A)      (A.random)   (B)    (B.new)
            if tmp.random:
                tmp.next.random = tmp.random.next
            else:
                tmp.next.random = None
            tmp = tmp.next.next
        
        #3.第三步，断链，分成两个链表
        tmp = pHead
        newHead = pHead.next
        newNode = pHead.next
        while tmp:
            #只要newNode.next的下一个结点还存在，就继续
            #让A指向B
            tmp.next = newNode.next
            
            if newNode.next:
                #让A.new指向 B.new
                newNode.next = tmp.next.next
            else:
                newNode.next = None
            tmp = tmp.next
            newNode = newNode.next
        return newHead
```



## 25.输入一个字符串,按字典序打印出该字符串中字符的所有排列。例如输入字符串abc,则打印出由字符a,b,c所能排列出来的所有字符串abc,acb,bac,bca,cab和cba。

思路：这一题没什么思路，主要是理解网上别人写的方法

用到了Python中集合这种数据结构的一些特性，比方说集合是必定不重复的

```python
class Solution:
    def Permutation(self, ss):
        if not ss:
            return []
        res = []
        self.helper(ss, res, '')
        return sorted(list(set(res)))

    def helper(self, ss, res, path):
        if not ss:
            res.append(path)
        else:
            for i in range(len(ss)):
                self.helper(ss[:i] + ss[i+1:], res, path + ss[i])
```

## 26.数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0

思路1：空间换时间，最容易想到的，注意字典的使用语法

```python
class Solution:
    def MoreThanHalfNum_Solution(self, numbers):
        # write code here
        #0.异常处理
        if not numbers:
            return 0
        
        
        #1.空间换时间
        dic = {}
        
        for i in range(len(numbers)):
            if dic.has_key(numbers[i]):
                dic[numbers[i]] += 1
            else:
                dic[numbers[i]] = 1
                
            if dic[numbers[i]] > (len(numbers)/2):
                return numbers[i]
        return 0
```

思路2：较为巧妙，权值抵消法，仅供参考

```python
class Solution:
    def MoreThanHalfNum_Solution(self, numbers):
        # write code here
        #0.异常处理
        if not numbers:
            return 0
        if len(numbers) == 1:
            return numbers[0]
        
        #1.设定一个基准值
        pivot = numbers[0]
        pivotVal = 1
        
        for i in range(1,len(numbers)):
            if numbers[i] == pivot:
                pivotVal += 1
                if pivotVal > (len(numbers)/2):
                    return pivot
            else:
                pivotVal -= 1
                if pivotVal == 0:
                    pivot = numbers[i]
                    pivotVal = 1
        if pivotVal > 1:
            return pivot
        else:
            return 0
```

## 27.输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4,。

思路：排序，返回。主要考虑不同的排序算法，这里选择快速排序和堆排序

快速排序

```python
# -*- coding:utf-8 -*-
class Solution:
    def GetLeastNumbers_Solution(self, tinput, k):
        # write code here
        #0.异常处理
        if not tinput:
            return []
        if k > len(tinput):
            return []
        
        #1.快排排序
        s = self.QuickSort(tinput, 0, len(tinput)-1)
        
        return s[:k]
        
    def QuickSort(self, s, low, high):
        #如果低位仍然比高位低，则继续找pivot值
        if low < high:
            pivot = self.Partition(s, low, high)
            
            #左半部分
            self.QuickSort(s, low, pivot-1)
            #右半部分
            self.QuickSort(s, pivot+1, high)
        return s
    
    def Partition(self, s, low, high):
        pivotVal = s[low]
        
        while low < high:
            #从右往左找，找到第一个比pivot小的
            while low < high and s[high] >= pivotVal:
                high -= 1
            s[low] = s[high]
            #从左往右找，找到第一个比pivot大的
            while low < high and s[low] <= pivotVal:
                low += 1
            s[high] = s[low]
            
        s[low] = pivotVal
        return low
        
        
```

堆排序：TBD



## 28.HZ偶尔会拿些专业问题来忽悠那些非计算机专业的同学。今天测试组开完会后,他又发话了:在古老的一维模式识别中,常常需要计算连续子向量的最大和,当向量全为正数的时候,问题很好解决。但是,如果向量中包含负数,是否应该包含某个负数,并期望旁边的正数会弥补它呢？例如:{6,-3,-2,7,-15,1,2,2},连续子向量的最大和为8\(从第0个开始,到第3个为止\)。给一个数组，返回它的最大连续子序列的和，你会不会被他忽悠住？\(子向量的长度至少是1\)

思路1：与前面的一个类似找到大于数组长度一半的题类似，可以从数组的性质出发

```python
# -*- coding:utf-8 -*-
class Solution:
    def FindGreatestSumOfSubArray(self, array):
        # write code here
        #0.异常处理
        if not array:
            return None
        
        #定义最大值和SUM,注意这里不能用0作为初始值，而要用第一个元素
        SUM = array[0]
        MAX = array[0]
        
        #1.遍历，不断求和，求最值，并比较
        for i in range(1, len(array)):
            #如果SUM值小于0,说明前面白算了，从新的开始吧
            if SUM <= 0:
                SUM = array[i]
            #如果SUM值大于0，那就更新SUM
            elif SUM > 0:
                SUM += array[i]
            #然后看SUM和MAX之间的大小关系，看看需不需要更新MAX
            if SUM > MAX:
                MAX = SUM
        return MAX
                
```

思路2：动态规划

TBD

## 29.求出1~13的整数中1出现的次数,并算出100~1300的整数中1出现的次数？为此他特别数了一下1~13中包含1的数字有1、10、11、12、13因此共出现6次,但是对于后面问题他就没辙了。ACMer希望你们帮帮他,并把问题更加普遍化,可以很快的求出任意非负整数区间中1出现的次数（从1 到 n 中1出现的次数）。

思路1：暴力破解

```python
# -*- coding:utf-8 -*-
class Solution:
    def NumberOf1Between1AndN_Solution(self, n):
        # write code here
        #0.异常处理
        if not n or n < 1:
            return 0
        str1=[]
        #1.暴力破解
        for i in range(n+1):
            str1.append(str(i))
        
        return ''.join(str1).count('1')
```

## 30.输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。

思路：参考了别人的思路，其实是自己定义自己的一个cmp函数，然后对这个数组排序

```python
from functools import cmp_to_key
class Solution:
    def PrintMinNumber(self, numbers):
        # write code here
        #0.异常处理
        if not numbers:
            return ''
        
        if len(numbers) < 2:
            return numbers[0]
        s = sorted(numbers, key=cmp_to_key(self.myCmp))
        result=''
        for i in range(len(s)):
            result += str(s[i])
        return result
            
        
    #1.自定义一个比较函数，进来两个数，比较一前一后哪个更大
    def myCmp(self, a, b):
        str1 = str(a)+str(b)
        str2 = str(b)+str(a)
        
        if str1 > str2:
            return 1
        elif str1 < str2:
            return -1
        else:
            return 0
```

## 32.在一个字符串\(0&lt;=字符串长度&lt;=10000，全部由字母组成\)中找到第一个只出现一次的字符,并返回它的位置, 如果没有则返回 -1（需要区分大小写）.（从0开始计数）

思路：空间换时间，初始化为256个空的列表（类似于int数组）然后利用Index对应的ASCII数字做记录 值得记住的两个函数 char-&gt;ASCII ord\(\), ASCII-&gt;char chr\(\)

或者使用字典

这里我第一次思考时存在一个误区，以为字典由于无序不能使用，实际上第一遍遍历完字符串后，第二次再遍历一次字符串就行了，遍历的不是字典，所以与有序无序无关

```python
class Solution:
    def FirstNotRepeatingChar(self, s):
        # write code here
        #0.异常处理
        if not s:
            return -1
        
        #1.空间换时间，但是由于题目中需要的是第一次出现，而字典是无序的，这里使用
        #类似C语言的数组的空序列作为载体
        
        charList = [0]*256
        
        #2.遍历这个字符串，把他们转换为ASCII码然后逐个写入有序数组对应的Index中
        sList = list(s)
        for i in range(len(sList)):
            charList[ord(sList[i])] += 1
            
        for i in range(len(sList)):
            if charList[ord(sList[i])] == 1:
                return i
        return -1
```

```python
# -*- coding:utf-8 -*-
class Solution:
    def FirstNotRepeatingChar(self, s):
        # write code here
        #0.异常处理
        if not s:
            return -1
        
        #1.利用字典，空间换时间
        dic = {}
        
        sList = list(s)
        for i in range(len(sList)):
            if dic.has_key(sList[i]):
                dic[sList[i]] += 1
            else:
                dic[sList[i]] = 1
        
        #2.第二次遍历这个字符串
        for i in range(len(sList)):
            if dic[sList[i]] == 1:
                return i
            
        return -1
```

## 35.统计一个数字在排序数组中出现的次数。

思路：TopK问题，采用递归和循环实现的二分法分别来寻找第一次和最后一次出现的k值

```python
# -*- coding:utf-8 -*-
class Solution:
    def GetNumberOfK(self, data, k):
        # write code here
        #0.异常处理
        if not data:
            return None

        #1.本质是TopK问题，找出第一次出现和最后一次出现的位置，相减即可
        # 本次将分别使用递归和循环分别解决
        low = 0
        high = len(data)-1
        s = self.getFirstK(data, low, high, k)
        b = self.getLastK(data, low, high, k)
        if s == b == None:
            return 0
        else:
            return b-s+1
    '''
    #递归
    def getFirstK(self, data, low, high, k):
        #计算mid值
        mid = (low + high) // 2
        #只要low还比high低
        if low <= high:
            #data[mid]比k值大
            if data[mid] > k:
                high = mid - 1
                return self.getFirstK(data, low, high, k)
            #data[mid]比k值小
            elif data[mid] < k:
                low = mid + 1
                return self.getFirstK(data, low, high, k)
            #data[mid]与K值一样,找前面还有没有
            elif data[mid] == k:
                #如果前面还有，还需要继续递归
                if data[mid-1] == k:
                    high = mid -1
                    return self.getFirstK(data, low, high, k)
                #如果前面没了，或者mid-1不是k了,return
                elif mid == 0 or data[mid - 1] != k:
                    return mid
    '''
    def getFirstK(self, data, low, high, k):
        while low <= high:
            mid = (low + high)// 2
            if data[mid] < k:
                low = mid+1
            elif data[mid] > k:
                high = mid -1
            elif data[mid] == k:
                if mid == 0 or data[mid-1] != k:
                    return mid
                else:
                    high = mid-1

    #循环
    def getLastK(self, data, low, high, k):
        while low <= high:
            mid = (low + high)// 2
            if data[mid] < k:
                low = mid+1
            elif data[mid] > k:
                high = mid -1
            elif data[mid] == k:
                if mid == len(data)-1 or data[mid+1] != k:
                    return mid
                else:
                    low = mid+1
```

## 36.输入一棵二叉树，求该树的深度。从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。

思路1：与之前的深度遍历类似，修改了最终变量的判断，不是路径的和，而是路径的长度，此长度即为深度

```python
class Solution:
    def TreeDepth(self, pRoot):
        # write code here
        #之前有一道，利用深度优先遍历，求路径的题，与这道类似，思想类似
        #定义结果
        result = [0]
        #0.异常处理
        if not pRoot:
            return 0
        
        #路径
        path = [pRoot.val]
        
        #1.递归调用
        self.DFS(pRoot, result, path)
        
        return result[0]
        
    def DFS(self, pRoot, result, path):
        #2.如果左子树右子树都没有了，说明到了叶子,判断这个path这个数组的长度，即为深度
        if not pRoot.left and not pRoot.left:
            if len(path) > result[0]:
                result[0] = len(path)
        #3.如果左子树还有
        if pRoot.left:
            #继续深入
            self.DFS(pRoot.left, result, path+[pRoot.left.val])
        #4.如果右子树还有
        if pRoot.right:
            self.DFS(pRoot.right, result, path+[pRoot.right.val])
```

思路2：看看别人写的

```python
class Solution:
    def TreeDepth(self, pRoot):
 
        if pRoot==None:
               return 0
        return max(self.TreeDepth(pRoot.left),self.TreeDepth(pRoot.right))+1
```

## 37.输入一棵二叉树，判断该二叉树是否是平衡二叉树。在这里，我们只需要考虑其平衡性，不需要考虑其是不是排序二叉树

思路：我再次使用了DFS的方法来遍历左右两颗子树的深度，求两颗左右子树的差值，来判断是否平衡

```python
class Solution:
    def IsBalanced_Solution(self, pRoot):
        # write code here
        #0.异常处理
        if not pRoot:
            return True
        if pRoot == {}:
            return True
        
        #1.对左子树与右子树分别求最大深度,计算差值
        LeftResult = [0]
        RightResult = [0]
        
        if pRoot.left:
            path1 = [pRoot.left]
            self.MaxDeep(pRoot.left, path1, LeftResult)

        if pRoot.right:
            path2 = [pRoot.right]
            self.MaxDeep(pRoot.right, path2, RightResult)

        if abs(LeftResult[0] - RightResult[0]) > 1:
            return False
        else:
            return True
        
    def MaxDeep(self, root, path, result):
        if not root.left and not root.right:
            if result[0] < len(path):
                result[0] = len(path)
        if root.left:
            self.MaxDeep(root.left, path+[root.left], result)
        if root.right:
            self.MaxDeep(root.right, path+[root.right], result)
```

## 38.一个整型数组里除了两个数字之外，其他的数字都出现了两次。请写程序找出这两个只出现一次的数字。

思路1：老办法，利用字典空间换时间

```python
# -*- coding:utf-8 -*-
class Solution:
    # 返回[a,b] 其中ab是出现一次的两个数字
    def FindNumsAppearOnce(self, array):
        # write code here
        #字典，空间换时间
        #0.异常处理
        if not array:
            return []
        
        #1.遍历 O(n)
        mydic={}
        for i in array:
            if mydic.has_key(i):
                mydic[i] += 1 
            else:
                mydic[i] =1
        result=[]
        for j in mydic.keys():
            if mydic[j] == 1:
                result.append(j)
        
        return result
```

## 39.小明很喜欢数学,有一天他在做数学作业时,要求计算出9~16的和,他马上就写出了正确答案是100。但是他并不满足于此,他在想究竟有多少种连续的正数序列的和为100\(至少包括两个数\)。没多久,他就得到另一组连续正数和为100的序列:18,19,20,21,22。现在把问题交给你,你能不能也很快的找出所有和为S的连续正数序列? Good Luck!

思路：参考讨论区的双指针滑动窗口方法

```python
# -*- coding:utf-8 -*-
class Solution:
    def FindContinuousSequence(self, tsum):
        # write code here
        #0.异常处理
        if tsum < 3:
            return []
        
        #1.双指针滑动窗口
        #注意，连续正数序列,从1开始
        low = 1
        high = 2
        result=[]
        tmp=[]
        #当这个滑动窗口还没越界，可以接着移动
        while low < high:
            #等差数列求和公式 (a0 + an)*n/2
            mysum = (low + high)*(high-low+1)/2
            #如果求和比实际的小
            if mysum < tsum:
                #那右边的就加大
                high += 1
            elif mysum > tsum:
                low += 1
            #如果求和一致
            else:
                #添加结果
                tmp=[]
                for i in range(low, high+1):
                    tmp.append(i)
                result.append(tmp)
                low += 1
                
        return result
                
```

## 40.输入一个递增排序的数组和一个数字S，在数组中查找两个数，使得他们的和正好是S，如果有多对数字的和等于S，输出两个数的乘积最小的。

思路:左右夹逼

```python
# -*- coding:utf-8 -*-
class Solution:
    def FindNumbersWithSum(self, array, tsum):
        # write code here
        #0. 异常处理
        if not array:
            return []
        
        #1.注意，数组是递增的，从两边往中间夹逼
        low = 0
        high = len(array)-1
        
        while low <= high:
            #如果两数之和比tsum大，high-1
            if (array[low] + array[high]) > tsum:
                high -= 1
            elif (array[low] + array[high]) < tsum:
                low += 1
            else:
                return [array[low], array[high]]
        return []
```

## 41.汇编语言中有一种移位指令叫做循环左移（ROL），现在有个简单的任务，就是用字符串模拟这个指令的运算结果。对于一个给定的字符序列S，请你把其循环左移K位后的序列输出。例如，字符序列S=”abcXYZdef”,要求输出循环左移3位后的结果，即“XYZdefabc”。是不是很简单？OK，搞定它！

思路1：利用Python的一些特性

```python
# -*- coding:utf-8 -*-
class Solution:
    def LeftRotateString(self, s, n):
        return s[n:] + s[:n]
```

思路2：TBD

## 42.牛客最近来了一个新员工Fish，每天早晨总是会拿着一本英文杂志，写些句子在本子上。同事Cat对Fish写的内容颇感兴趣，有一天他向Fish借来翻看，但却读不懂它的意思。例如，“student. a am I”。后来才意识到，这家伙原来把句子单词的顺序翻转了，正确的句子应该是“I am a student.”。Cat对一一的翻转这些单词顺序可不在行，你能帮助他么？

思路1：直接调用一些封装好的功能

```python
# -*- coding:utf-8 -*-
class Solution:
    def ReverseSentence(self, s):
        # write code here
        #0.异常处理
        if not s:
            return ''
        
        L = s.split(' ')
        return ' '.join(L[::-1])
        
```

思路2：利用栈的特性，自己实现

TBD

## 43.LL今天心情特别好,因为他去买了一副扑克牌,发现里面居然有2个大王,2个小王\(一副牌原本是54张^\_^\)...他随机从中抽出了5张牌,想测测自己的手气,看看能不能抽到顺子,如果抽到的话,他决定去买体育彩票,嘿嘿！！“红心A,黑桃3,小王,大王,方片5”,“Oh My God!”不是顺子.....LL不高兴了,他想了想,决定大\小 王可以看成任何数字,并且A看作1,J为11,Q为12,K为13。上面的5张牌就可以变成“1,2,3,4,5”\(大小王分别看作2和4\),“So Lucky!”。LL决定去买体育彩票啦。 现在,要求你使用这幅牌模拟上面的过程,然后告诉我们LL的运气如何， 如果牌能组成顺子就输出true，否则就输出false。为了方便起见,你可以认为大小王是0。

思路：按部就班地列出限制条件

比如 

1.如果是顺子，那么这组数的最大值与最小值的差值肯定小于5（由于大小王视为0，所以是小于\)

2.数组的长度必为5

3.数组必没有除了0之外的重复数字

保证了这3 点，即可说明这个数组可构成顺子

```python
# -*- coding:utf-8 -*-
class Solution:
    def IsContinuous(self, numbers):
        # write code here
        #1.长度必为5，否则False
        if len(numbers) != 5:
            return False
        
        #2.除了0之外没有重复的数字,并在这一次遍历中找到最大最小值
        dic={}
        miniVar = 14
        maxVar = -1
        for i in range(len(numbers)):
            if dic.has_key(numbers[i]) and numbers[i] != 0:
                return False
            elif numbers[i] != 0:
                dic[numbers[i]] = 1
                if numbers[i] > maxVar:
                    maxVar = numbers[i]
                if numbers[i] < miniVar:
                    miniVar = numbers[i]
                    
         #3.最大值－最小值必小于5
        if maxVar - miniVar < 5:
            return True
        else:
            return False
```

## 44.每年六一儿童节,牛客都会准备一些小礼物去看望孤儿院的小朋友,今年亦是如此。HF作为牛客的资深元老,自然也准备了一些小游戏。其中,有个游戏是这样的:首先,让小朋友们围成一个大圈。然后,他随机指定一个数m,让编号为0的小朋友开始报数。每次喊到m-1的那个小朋友要出列唱首歌,然后可以在礼品箱中任意的挑选礼物,并且不再回到圈中,从他的下一个小朋友开始,继续0...m-1报数....这样下去....直到剩下最后一个小朋友,可以不用表演,并且拿到牛客名贵的“名侦探柯南”典藏版\(名额有限哦!!^\_^\)。请你试着想下,哪个小朋友会得到这份礼品呢？\(注：小朋友的编号是从0到n-1\) 如果没有小朋友，请返回-1

思路：约瑟夫环问题。参考文章[https://cloud.tencent.com/developer/article/1164728](https://cloud.tencent.com/developer/article/1164728)

解法1 数学分析

```python
# -*- coding:utf-8 -*-
class Solution:
    def LastRemaining_Solution(self, n, m):
        # write code here
        #0.异常处理
        if n < 1:
            return -1
        
        #1.数学分析法
        out = 0
        for i in range(2, n+1):
            out = (out + m)%i
        return out
```

解法2：用数组模拟这个过程，复杂度高一点，但是容易理解

```python
# -*- coding:utf-8 -*-
class Solution:
    def LastRemaining_Solution(self, n, m):
        # write code here
        #0.异常处理
        if n < 1:
            return -1
        
        #1.定义一个[0,1,...,n]的数组,定义一个指向该删除的小朋友的指针
        array = list(range(n))
        pCur=0
        #2.进入一个循环，模拟删除小朋友
        #只要小朋友的个数还大于1，就继续
        while len(array) > 1:
            #3.计算出该出列的小朋友的位置
            #[0,1,2,3,4] m=3 n=5
            #[0,1,3,4]
            #[2,3,4]
            #[3]
            #注意这里的括号里要有pCur+,否则每次都是重新开始了
            pCur=(pCur+m-1)%len(array)
            array.pop(pCur)
        return array[0]
```

## 45.求1+2+3+...+n，要求不能使用乘除法、for、while、if、else、switch、case等关键字及条件判断语句（A?B:C）。

思路：&&短路功能

```python
# -*- coding:utf-8 -*-
class Solution:
    def Sum_Solution(self, n):
        # write code here
        return (n and (n+self.Sum_Solution(n-1)))
```

## 46.写一个函数，求两个整数之和，要求在函数体内不得使用+、-、\*、/四则运算符号。

思路：这一次使用C/C++来写思路如注释

```cpp
class Solution {
public:
    int Add(int num1, int num2)
    {
        //假设6+4  6: 110 
        //        4: 100
        //按位与 6&4<<1: 1000 , 这个得到的是需要进位的位置，左移1，就是进位
        //按位异或6^4: 010, 这个得到的是可以直接加减的位置
        //这个例子中，两个结果相或就是正确答案了，但是仍然存在相与为1的情况，这个时候要重复上面的步骤
        int n1,n2;
        n1 = (num1 & num2) << 1;
        n2 = num1^num2;
            
        while(n1&n2)
        {
            num1 = n1;
            num2 = n2;
            n1 = (num1 & num2) << 1;
            n2 = num1^num2;
        }
        return n1|n2;
    }
};
```

## 47.将一个字符串转换成一个整数，要求不能使用字符串转换整数的库函数。 数值为0或者字符串不是一个合法的数值则返回0

```text
输入一个字符串,包括数字字母符号,可以为空
如果是合法的数值表达则返回该数字，否则返回0
输入
+2147483647
1a33
输出
2147483647
0
```

思路：借用了一下字典，具体思路直接看代码即可

```python
# -*- coding:utf-8 -*-
class Solution:
    def StrToInt(self, s):
        # write code here
        #0.异常处理
        if not s:
            return 0
        
        #1.定义一个字典来处理对应关系
        numDic = {'0':0,
                  '1':1,
                  '2':2,
                  '3':3,
                  '4':4,
                  '5':5,
                  '6':6,
                  '7':7,
                  '8':8,
                  '9':9,}
        result=0
        Flag=1
        if s[0] == '+':
            Flag = 1
        elif s[0] == '-':
            Flag = -1
        else:
            result = result*10 + numDic[s[0]]
        #2.开始遍历
        for i in range(1,len(s)):
            #异常字符的处理
            if s[i] not in numDic:
                return 0
            else:
                result = result*10 + numDic[s[i]]
        return result*Flag
```

## 48.在一个长度为n的数组里的所有数字都在0到n-1的范围内。 数组中某些数字是重复的，但不知道有几个数字是重复的。也不知道每个数字重复几次。请找出数组中任意一个重复的数字。 例如，如果输入长度为7的数组{2,3,1,0,2,5,3}，那么对应的输出是第一个重复的数字2。

思路：空间换时间

```python
# -*- coding:utf-8 -*-
class Solution:
    # 这里要特别注意~找到任意重复的一个值并赋值到duplication[0]
    # 函数返回True/False
    def duplicate(self, numbers, duplication):
        # write code here
        #0.异常处理
        if not numbers:
            return False
        
        #1.空间换时间
        dic = {}
        
        for i in range(len(numbers)):
            if dic.has_key(numbers[i]):
                dic[numbers[i]] += 1
                if dic[numbers[i]] == 2:
                    duplication[0] = numbers[i]
                    return True
                    
            else:
                dic[numbers[i]] = 1
                
        return False
        
```

思路：不需要额外的数组或者hash table来保存，题目里写了数组里数字的范围保证在0 ~ n-1 之间，所以可以利用现有数组设置标志，当一个数字被访问过后，可以设置对应位上的数 + n，之后再遇到相同的数时，会发现对应位上的数已经大于等于n了，那么直接返回这个数即可。

## 49.给定一个数组A\[0,1,...,n-1\],请构建一个数组B\[0,1,...,n-1\],其中B中的元素B\[i\]=A\[0\]_A\[1\]_..._A\[i-1\]_A\[i+1\]_..._A\[n-1\]。不能使用除法。（注意：规定B\[0\] = A\[1\]  _A\[2\]_  ...  _A\[n-1\]，B\[n-1\] = A\[0\]_  A\[1\]  _..._  A\[n-2\];）

思路：参考别人  
  
`解释下代码，设有数组大小为5。对于第一个for循环第一步：b[0] = 1;   第二步：b[1] = b[0] * a[0] = a[0]                          第三步：b[2] = b[1] * a[1] = a[0] * a[1];                       第四步：b[3] = b[2] * a[2] = a[0] * a[1] * a[2];            第五步：b[4] = b[3] * a[3] = a[0] * a[1] * a[2] * a[3];`  

`然后对于第二个for循环`

`第一步temp *= a[4] = a[4]; b[3] = b[3] * temp = a[0] * a[1] * a[2] * a[4];`

`第二步temp *= a[3] = a[4] * a[3];b[2] = b[2] * temp = a[0] * a[1] * a[4] * a[3];`

`第三步temp *= a[2] = a[4] * a[3] * a[2]; b[1] = b[1] * temp = a[0] * a[4] * a[3] * a[2];`

`第四步temp *= a[1] = a[4] * a[3] * a[2] * a[1]; b[0] = b[0] * temp = a[4] * a[3] * a[2] * a[1];`

`由此可以看出从b[4]到b[0]均已经得到正确计算。`

```python
# -*- coding:utf-8 -*-
class Solution:
    def multiply(self, A):
        # write code here
        #0.异常处理
        if not A:
            return None
        
        result=[]
        result.append(1)
        
        for i in range(len(A)-1):
            result.append(result[i]*A[i])
            
        tmp=1
        
        for i in range(len(A)-1, -1, -1):
            result[i] *= tmp
            tmp *= A[i]
            
        return result
```



## 52.请实现一个函数用来找出字符流中第一个只出现一次的字符。例如，当从字符流中只读出前两个字符"go"时，第一个只出现一次的字符是"g"。当从该字符流中读出前六个字符“google"时，第一个只出现一次的字符是"l"。

思路：空间换时间,前面有很多题都非常类似

```python
# -*- coding:utf-8 -*-
class Solution:
    def __init__(self):
        self.charList = []
        self.searchMap = {}
        #self.finalIndex = 0
    # 返回对应char
    def FirstAppearingOnce(self):
        for i in range(len(self.charList)):
            if self.searchMap[self.charList[i]] == 1:
                return self.charList[i]
        return '#'
        
    def Insert(self, char):
        # write code here
        self.charList.append(char)
        #self.finalIndex = len(charList)
        if self.searchMap.has_key(char):
            self.searchMap[char] += 1
        else:
            self.searchMap[char] = 1
```

## 53.给一个链表，若其中包含环，请找出该链表的环的入口结点，否则，输出null。

思路：参考了别人的结论：  
  
`两个指针一个fast、一个slow同时从一个链表的头部出发，fast一次走2步，slow一次走一步，如果该链表有环，两个指针必然在环内相遇，此时只需要把其中的一个指针重新指向链表头部，另一个不变（还在环内），这次两个指针一次走一步，相遇的地方就是入口节点。`

```python
# -*- coding:utf-8 -*-
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None
class Solution:
    def EntryNodeOfLoop(self, pHead):
        # write code here
        #0.异常处理
        if not pHead or pHead.next == None:
            return None
        
        #一个快指针，一个慢指针
        pFast = pHead
        pSlow = pHead
        
        #快的每次走两步，慢的每次走一步
        pFast = pFast.next.next
        pSlow = pSlow.next
        while pSlow != pFast:
            pFast = pFast.next.next
            pSlow = pSlow.next
            
        #相遇了之后，一个留在圈内，一个回到原点，同时走一步
        pFast = pHead
        
        while pSlow != pFast:
            pFast = pFast.next
            pSlow = pSlow.next
        
        return pSlow
```

## 54.在一个排序的链表中，存在重复的结点，请删除该链表中重复的结点，重复的结点不保留，返回链表头指针。 例如，链表1-&gt;2-&gt;3-&gt;3-&gt;4-&gt;4-&gt;5 处理后为 1-&gt;2-&gt;5

思路：本题需要注意一个细节，重复的全部都要删掉，所以需要定义一个新的头结点，防止全部都要删掉，剩下的就是2个指针，一前一后，全部删除

```python
# -*- coding:utf-8 -*-
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None
class Solution:
    def deleteDuplication(self, pHead):
        # write code here
        #0.异常处理
        if not pHead or pHead.next == None:
            return pHead
        
        #1.设置两个指针,由于重复的结点不保留，还需要定义一个新的结点
        pNewHead = ListNode(0)
        pNewHead.next = pHead
        #new->1->2->3->3->4->4->5
        p1 = pNewHead
        p2 = pNewHead.next
        #new->1->2->3->3->4->4->5
        #    p1 p2
        #2.p2走到尽头才跳出
        while p2 != None:
            #如果重复了，说明要删除了，往下找到第一个不一样的
            if p2.next != None and p2.val == p2.next.val:
                while p2.next != None and p2.val == p2.next.val:
                    p2 = p2.next
                p1.next = p2.next
                p2 = p2.next
            #P2作为哨兵指针，来探路，如果P2和P2的下一个都是不重复的，可以让P1走
            else:
                p1 = p1.next
                p2 = p2.next
        return pNewHead.next
```

## 55.给定一个二叉树和其中的一个结点，请找出中序遍历顺序的下一个结点并且返回。注意，树中的结点不仅包含左右子结点，同时包含指向父结点的指针。

思路：这道题最好自己画一个树出来，分类讨论出可能的情况，做好判断即可

（1） 若该节点存在右子树：则下一个节点为右子树最左子节点

 （2） 若该节点不存在右子树：这时分两种情况：

 2.1 该节点为父节点的左子节点，则下一个节点为其父节点

 2.2 该节点为父节点的右子节点，则沿着父节点向上遍历，直到找到一个节点的父节点的左子节点为该节点，则该节点的父节点下一个节点

```python
# -*- coding:utf-8 -*-
# class TreeLinkNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
#         self.next = None
class Solution:
    def GetNext(self, pNode):
        # write code here
        #0.异常处理
        if not pNode:
            return None
        
        #1.如果有右子树
        if pNode.right != None:
            #指向这个右子树
            pNode = pNode.right
            #然后找到最左子树
            while pNode.left != None:
                pNode = pNode.left
            return pNode
        #2.如果没有右子树
        else:
            #如果是根节点
            if pNode.next == None:
                return None
            #如果是左叶子节点
            elif pNode.next.left == pNode:
                return pNode.next
            #如果是右叶子节点
            elif pNode.next.right == pNode:
                #找到第一个是父节点左子节点的节点
                while pNode.next != None:
                    pNode = pNode.next
                    #注意这里一定要判断pNode.next != None,否则可能会访问根节点的next.left,导致出错
                    if pNode.next != None and pNode == pNode.next.left:
                        return pNode.next
                #全都找完了还没找到,那就是最后一个节点了
                return None        
```

## 56.请实现一个函数，用来判断一颗二叉树是不是对称的。注意，如果一个二叉树同此二叉树的镜像是同样的，定义其为对称的。

思路1：递归解决，比较清晰直观

```python
# -*- coding:utf-8 -*-
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
class Solution:
    def isSymmetrical(self, pRoot):
        # write code here
        #0.异常处理
        if not pRoot:
            return True
        
        #1.递归完成比较
        return self.myCmp(pRoot.left, pRoot.right)
        
    def myCmp(self, pRoot1, pRoot2):
        #2.如果都已到了尽头
        if not pRoot1 and not pRoot2:
            return True
        #3.只有其中一个到了尽头
        if not pRoot1 or not pRoot2:
            return False
        #4.值相等，继续递归
        if pRoot1.val == pRoot2.val:
            return self.myCmp(pRoot1.left, pRoot2.right) and self.myCmp(pRoot1.right, pRoot2.left)

```

## 57.请实现一个函数按照之字形打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右至左的顺序打印，第三行按照从左到右的顺序打印，其他行以此类推。

思路：前面有一道层序遍历二叉树的问题，稍微改一下，就是题解了

```python
# -*- coding:utf-8 -*-
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
class Solution:
    def Print(self, pRoot):
        # write code here
        #0.异常处理
        if not pRoot:
            return []
        
        #1.层序遍历
        result = []
        oddNode = []
        oddNode.append(pRoot)
        evenNode = []
        val = []
        
        while oddNode != [] or evenNode != []:
            #奇数层，从前往后读
            for i in range(len(oddNode)):
                val.append(oddNode[i].val)
            result.append(val)
            val=[]
            #整理出下一层的节点
            while oddNode != []:
                tmpNode = oddNode.pop(0)
                if tmpNode.left:
                    evenNode.append(tmpNode.left)
                if tmpNode.right:
                    evenNode.append(tmpNode.right)
            #偶数层，从后往前读
            if evenNode != []:
                for i in range(len(evenNode)-1,-1,-1):
                    val.append(evenNode[i].val)
                result.append(val)
                val=[]
            #整理出下一层的节点
            while evenNode != []:
                tmpNode = evenNode.pop(0)
                if tmpNode.left:
                    oddNode.append(tmpNode.left)
                if tmpNode.right:
                    oddNode.append(tmpNode.right)
        return result
```

## 58.从上到下按层打印二叉树，同一层结点从左至右输出。每一层输出一行。

思路：改一下上道题的遍历书序顺序即可

```python
# -*- coding:utf-8 -*-
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
class Solution:
    def Print(self, pRoot):
        # write code here
        #0.异常处理
        if not pRoot:
            return []
        
        #1.层序遍历
        result = []
        oddNode = []
        oddNode.append(pRoot)
        evenNode = []
        val = []
        
        while oddNode != [] or evenNode != []:
            #奇数层，从前往后读
            for i in range(len(oddNode)):
                val.append(oddNode[i].val)
            result.append(val)
            val=[]
            #整理出下一层的节点
            while oddNode != []:
                tmpNode = oddNode.pop(0)
                if tmpNode.left:
                    evenNode.append(tmpNode.left)
                if tmpNode.right:
                    evenNode.append(tmpNode.right)
            #偶数层，还是从前往后读
            if evenNode != []:
                for i in range(len(evenNode)):
                    val.append(evenNode[i].val)
                result.append(val)
                val=[]
            #整理出下一层的节点
            while evenNode != []:
                tmpNode = evenNode.pop(0)
                if tmpNode.left:
                    oddNode.append(tmpNode.left)
                if tmpNode.right:
                    oddNode.append(tmpNode.right)
        return result
```

## 59.给定一棵二叉搜索树，请找出其中的第k小的结点。例如， （5，3，7，2，4，6，8） 中，按结点数值大小顺序第三小结点的值为4。

思路：中序遍历即为排序好的结果

```python
# -*- coding:utf-8 -*-
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
class Solution:
    # 返回对应节点TreeNode
    def KthNode(self, pRoot, k):
        # write code here
        #0.异常处理
        if not pRoot or not k:
            return None
        
        #1.二叉搜索树的中序遍历，即为排好序的列表
        self.result=[]
        self.Tin(pRoot, k)
        if len(self.result) < k:
            return None
        else:
            return self.result[k-1]
    
    def Tin(self,pRoot, k):
        if not pRoot or len(self.result) >= k:
            return
        
        #中序，左根右
        self.Tin(pRoot.left,k)
        self.result.append(pRoot)
        self.Tin(pRoot.right,k)
```

## 60.请实现两个函数，分别用来序列化和反序列化二叉树

二叉树的序列化是指：把一棵二叉树按照某种遍历方式的结果以某种格式保存为字符串，从而使得内存中建立起来的二叉树可以持久保存。序列化可以基于先序、中序、后序、层序的二叉树遍历方式来进行修改，序列化的结果是一个字符串，序列化时通过 某种符号表示空节点（\#），以 ！ 表示一个结点值的结束（value!）。

二叉树的反序列化是指：根据某种遍历顺序得到的序列化字符串结果str，重构二叉树。

例如，我们可以把一个只有根节点为1的二叉树序列化为"1,"，然后通过自己的函数来解析回这个二叉树



思路：前序遍历， 并根据前序遍历重构二叉树

```python
# -*- coding:utf-8 -*-
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
class Solution:
    def __init__(self):
        self.flag = -1
         
    def Serialize(self, root):
        # write code here
        if not root:
            return '#,'
        return str(root.val)+','+self.Serialize(root.left)+self.Serialize(root.right)
         
    def Deserialize(self, s):
        # write code here
        self.flag += 1
        l = s.split(',')
         
        if self.flag >= len(s):
            return None
        root = None
         
        if l[self.flag] != '#':
            root = TreeNode(int(l[self.flag]))
            root.left = self.Deserialize(s)
            root.right = self.Deserialize(s)
        return root
```

## Important 65.请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中的任意一格开始，每一步可以在矩阵中向左、右、上、下移动一格。如果一条路径经过了矩阵的某一格，那么该路径不能再次进入该格子。例如，在下面的3×4的矩阵中包含一条字符串“bfce”的路径（路径中的字母用加粗标出）。\[\["a","b","c","e"\], \["s","f","c","s"\], \["a","d","e","e"\]\] 但矩阵中不包含字符串“abfb”的路径，因为字符串的第一个字符b占据了矩阵中的第一行第二个格子之后，路径不能再次进入这个格子。

```python
# -*- coding:utf-8 -*-
class Solution:
    def hasPath(self, matrix, path):
        # write code here
        #0.异常处理
        if not matrix:
            return False
        if not path:
            return True
        
        def dfs(i, j, k):
            #3.如果路径越界，或者当前的字母不符合路径，返回False
            if not 0<= i < len(matrix[0]) or not 0<= j < len(matrix) or matrix[i][j] != path[k]:
                return False
            
            #4.执行到了这里
            if k == len(path)-1:
                return True
            
            #保存当前字母，继续DFS
            tmp , matrix[i][j] = matrix[i][j], '/'
            
            res = dfs(i+1, j, k+1) or dfs(i, j+1, k+1) or dfs(i-1, j, k+1) or dfs(i, j-1, k+1)
            
            #5.如果执行到了这里，说明递归返回了，我们还原上面的tmp到矩阵里面
            matrix[i][j] = tmp
            
            return res
        
        #1.定义两个循环,遍历这个二维数组
        for i in range(len(matrix)):
            for j in range(len(matrix[0])):
                #2.DFS
                if dfs(i, j, 0):
                    return True
        return False            
                
```



