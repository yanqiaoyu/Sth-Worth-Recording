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

