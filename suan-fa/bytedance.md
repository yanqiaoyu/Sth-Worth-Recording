---
description: '字节跳动高频算法题 参考https://www.nowcoder.com/discuss/445445'
---

# ByteDance

## LeetCode **179. 最大数**

思路1： 利用 functiontools  的cmp\_to\_key这个关键字，自定义sorted的方法，进行排序

由于我们要输出的是一个最大的数，显然，我们需要的是一个降序的数组，那么注意，在重写方法时，

若 x+y &gt; y+x 本来对于一个升序排列的方法来说，应该返回 1 ，但我们这里返回-1

这样记返回-1还是1：正常的是升序-&gt; 那传进来的 x,y 返回1的，大的放后面，对于逆序来说，小的放后面

```python
from functools import cmp_to_key
class Solution:
    def largestNumber(self, nums: List[int]) -> str:
        #0.异常处理
        if not nums:
            return ""
        elif nums == [0]*len(nums):
            return "0"

        #1.自定义排序方法
        def mykey(x, y):
            #我们本质上需要的是一个降序排列，所以这里返回的是-1而不是1
            if x+y > y+x:
                return -1
            elif x+y < y+x:
                return 1
            else:
                return 0
        #这里使用的map函数，意味着对于nums里面的每一个变量调用str()方法
        result = "".join(sorted(map(str, nums), key=cmp_to_key(mykey)))
        return result
```

思路2：重写类的魔法方法

其实上面的cmp\_to\_key从源码来分析，也是重写了比较大小的魔法方法，这样的话，我们其实也能自己重写魔法方法

```python
#1.重写魔法方法
#lt : less than gt: greater than
#显然，我们这里需要对小于做取反处理
#正常逻辑小于是 x+y < y+x
class mycmp(str):
    def __lt__(x, y):
        return x+y > y+x

class Solution:
    def largestNumber(self, nums: List[int]) -> str:
        #0.异常处理
        if not nums:
            return ""
        elif nums == [0]*len(nums):
            return "0"
        #注意这里mycmp是类，所以不需要()
        result = "".join(sorted(map(str, nums), key=mycmp))
        return result

```

## LeetCode **1. 两数之和：**

显然，这一题可以用字典来做

我们只需1次遍历，在遍历的过程中添加这样的条目: {数值}:{下标}

且每次遍历前，先判断 target - num\[i\] 在不在当前的数组里面，如果在说明找到了，返回

主要记住，先判断，再添加，找不到返回空

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        #method1:字典
        #0.异常处理
        if not nums:
            None

        #1.每次遍历，把{值}:{下标}存在对应的字典里
        mydic = {}
        for i in range(len(nums)):
            #如果(目标值-当前值)存在于当前字典，说明条件已经满足
            if (target - nums[i]) in mydic:
                return [mydic[target - nums[i]], i]
            #否则，添加进字典
            mydic[nums[i]] = i
        return []
```

## LeetCode **15. 三数之和：**给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有满足条件且不重复的三元组。注意：答案中不可以包含重复的三元组。

可真是把爷整懵了, 初看这道题，想到的是2数之和的变种， 毕竟 a+b+c = 0可以推倒为 a+b=-c，但是并不是最优方法

思路：

0.排序，这个直接 .sort\(\)算了

1.我们先固定一位，比方说p1在第一次循环中先指向nums\[0\]

2.第二个指针和第三个指针分别指向剩下数组的low 和 high

3.low逐渐右移

4.在这个过程中，注意去重，也要注意，如果p1已经大于0了，那么剩下的无需继续验证了，必然不可能等于0了（因为已经排好序了，后面都是大于0的了）

下面给出一个不会超时的答案

```python
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        #0.异常处理
        
        if nums == []:
            return []
        elif len(nums) < 3:
            return []
        elif nums == [0]*len(nums):
            return [[0,0,0]]
        
        
        #1.先对数组排序
        nums.sort()
        Length = len(nums)
        result = []

        #2.定义3个指针，第一个指针每轮固定
        #第二第三个指针分别指向low ， high
        #high 不断-- ，知道相遇或者符合题意
        #过程中记得去重
        for p1 in range(Length):
            if nums[p1] > 0:
                break
            #p1去重, p1不大于0，会导致溢出
            if p1 > 0 and nums[p1] == nums[p1 - 1]:
                #重复了，跳过此次循环
                continue
            p3 = Length - 1
            #p1没重复，开始走p2和p3
            for p2 in range(p1+1, Length):
                #p2去重
                if p2 > p1+1 and nums[p2] == nums[p2 - 1]:
                    continue
                #只要p3明显不符合要求，就左移
                while p3>p2 and nums[p2]+nums[p3] > -nums[p1]:
                    p3 -= 1
                if p2 == p3:
                    break
                if nums[p1] + nums[p2] + nums[p3] == 0:
                    result.append([nums[p1] , nums[p2] , nums[p3]]) 
        return result

```

重点关注Line 36 -41的代码，这里没写好无法AC

## LeetCode **3. 无重复字符的最长子串 :**给定一个字符串，请你找出其中不含有重复字符的 **最长子串** 的长度。

这是我为数不多的一次AC的题，主要思想：双指针做滑动窗口+字典去重

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        #0.异常处理
        if not s:
            return 0
        #双指针的滑动窗口
        start = end = 0
        res = {}
        max = 0
        while end < len(s):
            #只要右指针指向的元素还未出现，就添加进字典中
            if s[end] not in res:
                res[s[end]] = 1
                end += 1
            #如果右指针指向的元素已经出现了
            else:
                #缩短左指针，直到又不存在重复为止
                res[s[start]] -= 1
                #如果缩短后已经没了，就从res里面pop出这个元素
                if res[s[start]] == 0:
                    res.pop(s[start])
                #左指针继续走
                start += 1
            if len(res) > max:
                max = len(res)
        return max
```

## LeetCode 160.相交链表

又是一题能够自己想出思路的题

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:
        #没有参考题解，说说自己的思路
        #先走一遍 A B链表
        #计算出A B的长度
        #然后 计算出A与B的差值，让长的那个再开始走这个差值
        #此时，A B都应该距离相交节点距离等长
        #开始同时往后走，判断是否存在相同节点
        #0.异常处理
        if not headA or not headB:
            return None 
        #1.计算A B长度
        LengthA = 0
        LengthB = 0
        pA = headA
        pB = headB
        while pA:
            if pA:
                LengthA += 1
            if pA.next:
                pA = pA.next
            else:
                break
        while pB:
            if pB:
                LengthB += 1
            if pB.next:
                pB = pB.next
            else:
                break
        #2.计算差值,取绝对值
        d = abs(LengthA - LengthB)

        #3.让长的那个先走
        pTemp1 = headA if LengthA >= LengthB else headB
        for i in range(d):
            pTemp1 = pTemp1.next
        
        #4.然后长短一起走
        pTemp2 = headB if LengthA >= LengthB else headA
        while True:
            if pTemp1 ==  None and pTemp2 == None:
                return None
            elif pTemp1 == pTemp2 and pTemp1!=None:
                return pTemp1
            pTemp1 = pTemp1.next
            pTemp2 = pTemp2.next
```

我佛了，看完题解，别人的思路也太强了8，我哭了嗷

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:
        #题解思路：A走完A后去走B，B走完B后去走A，有缘终究相遇，无缘返回None
        #0.异常处理
        if not headA or not headB:
            return None 
        pA = headA
        pB = headB
        while pA != pB:
            pA = pA.next if pA else headB
            pB = pB.next if pB else headA
        return pA  
```

## 剑指 Offer 39. 数组中出现次数超过一半的数字

常规思路：HashMap

```python
class Solution:
    def majorityElement(self, nums: List[int]) -> int:
        dic = {}
        for i in range(len(nums)):
            if nums[i] not in dic:
                dic[nums[i]] = 1
            else:
                dic[nums[i]] += 1
            if dic[nums[i]] > len(nums) // 2:
                return nums[i]
```

更低时间复杂度的一种思路:投票法

设第一个为众数，往后遍历，相同+1 不同-1 ，直到变成0，设下一个数为众数，最后返回这个众数即可

如果需要验证是否是众数，只需再遍历一次判断个数是否大于长度的一半即可

```python
class Solution:
    def majorityElement(self, nums: List[int]) -> int:
        votes, count = 0, 0
        for num in nums:
            if votes == 0: 
                x = num
            #votes += 1 if num == x else -1
            if num == x:
                votes += 1
            else:
                votes -= 1
        return x
        '''
        # 验证 x 是否为众数
        for num in nums:
            if num == x: 
                count += 1
        return x if count > len(nums) // 2 else 0 # 当无众数时返回 0
        '''
```

## LeetCode 20.有效的括号

Easy题，又一次不参考题解写了出来

```python
class Solution:
    def isValid(self, s: str) -> bool:
        #看到这一题第一想到的用栈
        #注意这题只有特殊符号
        #0.异常处理，注意这里空字符串有效
        if not s:
            return True
        #如果长度为奇数，无需验证直接false
        if len(s)%2 == 1:
            return False
        #1.用栈来pop push
        result = []
        s = list(s)
        for i in range(len(s)):
            if s[i] == '(' or s[i] == '[' or s[i] == '{':
                result.append(s[i])
            else:
                if not self.PopUntilFind(result, s[i]):
                    return False
        #显然，如果最后result还有剩下的，那肯定不匹配
        return True if len(result) == 0 else False
                
    def PopUntilFind(self, result, char):
        if char == ')':
            for _ in result:
                match = result.pop()
                if match == '(':
                    return True
            return False
        elif char == ']':
            for _ in result:
                match = result.pop()
                if match == '[':
                    return True
            return False
        elif char == '}':
            for _ in result:
                match = result.pop()
                if match == '{':
                    return True
            return False
```

## LeetCode 236.二叉树的最近公共祖先

首先需要明确的一点，这种题本质上是一个后序遍历

其次，就是在后序遍历之中其他的语句完成题目所需要的判断

一次标准的后序遍历是什么样的？

```python
    def __init__(self):
        self.result=[]

    def postorderTraversal(self, root: TreeNode) -> List[int]:
        if not root:
            return []
        
        self.postorderTraversal(root.left)
        self.postorderTraversal(root.right)
        self.result.append(root.val)
        return self.result
```

当然，这道题目其实不需要记录遍历值，我们需要的是最近的一个公共祖先，所以修改如下

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        # 递归终止条件：当前节点为空，或者找到了p 和 q其中的一个
        if not root or root == p or root == q:
            return root
        
        # 递归主体：左 右
        left = self.lowestCommonAncestor(root.left, p, q)
        right = self.lowestCommonAncestor(root.right, p, q)

        # 递归后的判断
        #左也没有，右也没有，是叶子节点
        if not left and not right:
            return
        #只有左或者只有右
        if not left:
            return right
        if not right:
            return left
        #左右都有
        return root
        
```



## 剑指Offer 21.调整数组顺序使奇数位于偶数前面

如果用额外空间的话其实挺简单的

```python
class Solution:
    def exchange(self, nums: List[int]) -> List[int]:
        #自己的思路，长度为n的辅助栈
        if not nums:
            return []
        stack1=[]
        stack2=[]
        for i in nums:
            if i % 2 == 1:
                stack1.append(i)
            else:
                stack2.append(i)
        return stack1+stack2
```

下面用双端指针，不需要额外空间来做

```python
        if not nums:
            return []
        #双端指针
        low = 0
        high = len(nums)-1

        while low < high:
            #由于奇数在前，偶数在后，让low指针找到第一个偶数停下
            while low < high and nums[low] % 2 == 1:
                low += 1
            #让high指针找到第一个偶数停下
            while low < high and nums[high] % 2 == 0:
                high -= 1

            #交换位置
            nums[low], nums[high] = nums[high], nums[low]
        return nums
```

## LeetCode 19.删除链表的倒数第N个节点

按照双指针一个先走n，一个后开始走的思路即可，注意边界与异常值判读

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        #说说自己的思路，以示例的链表为例
        #定义两个指针，一个先走n步，另一个再开始走
        #等第一个走到了尽头，另一个也走到了需要被删除的结点的前面

        #0.异常处理
        if not head:
            return None
        
        #1.定义两个指针
        pFast = pSlow = head

        #2.fast先走n步
        for i in range(n):
            pFast = pFast.next
        
        #如果pFast此时已经None了
        #举例:[1,2] 2 或者 [1] 1
        if not pFast:
            pSlow = pSlow.next
            return pSlow

        #3.两个开始一起走,直到pFast走到最后一个结点
        while pFast.next:
            pFast = pFast.next
            pSlow = pSlow.next
        
        #4.此时pSlow已经走到了要被删除的结点的前面，准备删除
        pSlow.next = pSlow.next.next

        return head


```

## LeetCode 206.反转链表

迭代方法，只需要思考清楚断链的过程就很清晰了

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        #定义两个指针，一个指向虚构的一个Null节点，一个指向第1个节点
        #Null -> 1 -> 2 -> 3 -> 4 -> 5 -> Null
        # p1     p2(head)
        p1 = None
        p2 = head

        #0.考虑异常情况
        if not head:
            return None
        elif head.next == None:
            return head
        #考虑断链过程
        #1.head往前走
        #Null -> 1 -> 2
        # p1     p2   head
        #2.p2的节点的next指向p1
        #Null <- 1    2
        # p1    p2    head
        #3.p1 跑到p2这里
        #Null <- 1     2
        #      p2(p1) head
        #4.p2 跑到 head这里
        #Null <- 1     2
        #        p1   p2(head)
        #重复

        while p2:
            head = head.next
            p2.next = p1
            p1 = p2 
            p2 = head
        return p1
```

题目里还问了能否用递归来解决，有点小头疼

注意，这里面的递归返回的一直都是5，也就是最末尾的节点，因为这个节点最后要作为头结点返回

而中间那两句才是精髓

假设我们到最深后，第一次返回

现在是4，那么4.next.next = 4 ,就是5.next = 4

4.next = null ，就是断链

厉害了..

```python
class Solution(object):
	def reverseList(self, head):
		"""
		:type head: ListNode
		:rtype: ListNode
		"""
		# 递归终止条件是当前为空，或者下一个节点为空
		if(head==None or head.next==None):
			return head
		# 这里的cur就是最后一个节点
		cur = self.reverseList(head.next)
		# 这里请配合动画演示理解
		# 如果链表是 1->2->3->4->5，那么此时的cur就是5
		# 而head是4，head的下一个是5，下下一个是空
		# 所以head.next.next 就是5->4
		head.next.next = head
		# 防止链表循环，需要将head.next设置为空
		head.next = None
		# 每层递归函数都返回cur，也就是最后一个节点
		return cur
```

## LeetCode **9. 回文数**

两种办法，一种利用字符串的性质，直接得出结论，另一种运用数学办法算

```python
class Solution:
    def isPalindrome(self, x: int) -> bool:
        #0.依题意，负数肯定不是回文数
        if x < 0:
            return False
        elif x == 0:
            return True
        '''
        如果不使用字符串的方法
        return str(x) == str(x)[::-1]
        '''

        newInt = 0
        y = x
        
        while y:
            tmp = y % 10
            newInt = newInt*10 + tmp
            y = y // 10
        return newInt == x


```

## LeetCode  169. **多数元素**

经典思路，无须多言

```python
class Solution:
    def majorityElement(self, nums: List[int]) -> int:
        #method 1 空间换时间
        '''
        dic={}
        halflen = len(nums) // 2
        for i in nums:
            if i not in dic:
                dic[i] = 1
            else:
                dic[i] += 1
            if dic[i] > halflen:
                return i
        '''
        
        #method 2 投票
        #记第一个元素为1，往后走，相同+1，不同-1，为0则更换元素
        '''
        result = nums[0]
        count = 1
        for i in range(1, len(nums)):
            if count == 0:
                result = nums[i]
            if nums[i] == result:
                count += 1
            else:
                count -= 1
        return result
        '''
            
        
```

## LeetCode 215 数组中的第K个最大元素

经典的TopK问题，用到了经典的快速排序，或者堆，最最最基本的基本功了，不管怎么样都必须记住

先给出快速排序的写法

## LeetCode 796.旋转字符串

很有趣的问题，第一个方法参考了别人的精妙的想法 A+A必然囊括了所有B的可能性

第二个方法就是常规的遍历了

```python
class Solution:
    def rotateString(self, A: str, B: str) -> bool:
        '''
        method 1 直接利用语法特性判断 B在不在 A+A里面
        '''
        return len(A) == len(B) and B in (A+A)

        '''
        method 2 遍历判断: 只要在长度遍历完之前，跳出了循环，说明存在相同的字串
        '''
        while maxStep:
            A=A[1:]+A[0]
            if A==B:
                break
            maxStep-=1
        return maxStep>0
```

## LeetCode 543.二叉树的直径

这道题我一开始想的简单了，以为最大路径就是从根结点出发，求左子树的最大的深度和右子树的最大深度，但是实际上不是，因为最大路径不一定会经过根结点

看了一下别人的解答，其实这道题本质上还是求最大深度的问题，只是需要遍历这棵树，求的是**每一个**结点的最大左子树深度和最大右子树深度

可以接着做 124 和 687

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def __init__(self):
        self.max = 0

    def diameterOfBinaryTree(self, root: TreeNode) -> int:
        self.deepth(root)

        return self.max

    def deepth(self, root):
        if not root:
            return 0
        left = self.deepth(root.left)
        right = self.deepth(root.right)

        self.max = max(self.max, left+right)

        return max(left, right)+1
```

## LeetCode 704 二分查找

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        #有序数组，考虑二分
        high = len(nums) - 1
        low = 0

        while low < high:
            mid = (low + high) // 2
            if nums[mid] == target:
                return mid
            elif nums[mid] > target:
                high = mid - 1
            elif nums[mid] < target:
                low = mid + 1
        
        return low if nums[low] == target else -1
```

## LeetCode **25. K 个一组翻转链表**

虽然是Hard题，但是思路其实非常好理解，这里我们使用栈完成翻转

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def reverseKGroup(self, head: ListNode, k: int) -> ListNode:
        #定义一个虚拟的头结点
        #Fakepre -> 1 -> 2 -> 3 -> 4 -> 5
        #k个节点入栈 cur
        #Fakepre -> [1 -> 2 -> 3] -> 4 -> 5 -> Null
        #弹出                         cur
        #Fakepre -> [3 -> 2 -> 1] -> 4 -> 5 -> Null
        #                      p    cur
        FakePre = ListNode(-1)
        p = FakePre

        cur = head
        stack = []

        while cur:
            #k个节点入栈
            for count in range(k):
                stack.append(cur)
                cur = cur.next
                #剩余节点不足以构成反转链表
                if cur == None and count < k-1:
                    return FakePre.next
            
            #k个节点出栈
            while stack:
                p.next = stack.pop()
                p = p.next
            
            #出完栈后，p指向的其实是栈底元素，现在让他指向cur
            p.next = cur

        return FakePre.next
```



