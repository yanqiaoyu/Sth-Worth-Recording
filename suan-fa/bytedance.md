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

## LeetCode **1. 两数之和**

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

