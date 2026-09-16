# LeetCode 1. 两数之和｜从暴力枚举到真正利用哈希表

两数之和算是非常经典的一道题。题目大概是：

```text
给定一个整数数组 nums 和一个整数 target，

找出两个数字，使它们相加等于 target，

然后返回这两个数字的下标。
```

例如：

```python
nums = [2, 7, 11, 15]
target = 9
```

因为：

```text
2 + 7 = 9
```

所以返回：

```python
[0, 1]
```

这题我一开始其实已经想到：

> 好像可以用哈希表，因为可以同时保存下标和值。

但写着写着，我发现自己虽然确实创建了一个哈希表，实际上做的事情还是暴力枚举。


---
# 一、我最开始的思路：先把数字和下标存起来

因为题目最终要返回的是：

```text
下标
```

所以我第一反应是先建立一个字典：

```python
cal = {}
```

然后把：

```text
数字 → 下标
```

存进去。

例如：

```python
nums = [2, 7, 11, 15]
```

就会得到：

```python
cal = {
    2: 0,
    7: 1,
    11: 2,
    15: 3
}
```

于是我最开始写成了：

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:

        cal = {}
        
        for i in range(len(nums)):
            cal[nums[i]] = i

        for i in range(len(nums)):
            for j in range(i + 1, len(nums)):
                if nums[i] + nums[j] == target:
                    return [cal[nums[i]], cal[nums[j]]]
```

乍一看好像：

```text
用了哈希表
+
也能找到两个数字
```

但这里其实有两个问题。


---
# 二、第一个问题：既然已经有 i 和 j，为什么还要去字典找？

后面的双重循环已经是：

```python
for i in range(len(nums)):
    for j in range(i + 1, len(nums)):
```

如果：

```python
nums[i] + nums[j] == target
```

那答案其实已经很明确了：

```python
[i, j]
```

根本不需要再写：

```python
[cal[nums[i]], cal[nums[j]]]
```

因为 **i、j 本身就是我们需要的下标。**

所以如果继续按照这个思路写，哈希表甚至可以整个删掉：

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:

        for i in range(len(nums)):
            for j in range(i + 1, len(nums)):
                if nums[i] + nums[j] == target:
                    return [i, j]
```

这就是最直接的 **暴力解法**。

思路也很简单：

```text
拿第一个数字
↓
和后面的每一个数字相加
↓
不行
↓
换下一个数字
↓
继续和后面的数字相加
```

但它需要两层循环：

```text
O(n²)
```

---
# 三、第二个问题：字典的 key 会覆盖

我原来的哈希表是：

```python
cal[nums[i]] = i
```

也就是：

```text
key   = 数字
value = 下标
```

但是字典有一个特点：

> **key 是唯一的。**

比如：

```python
nums = [3, 3]
target = 6
```

第一次：

```python
cal[3] = 0
```

得到：

```python
{
    3: 0
}
```

第二次又出现一个 `3`：

```python
cal[3] = 1
```

前面的值就会被覆盖：

```python
{
    3: 1
}
```

但正确答案应该是：

```python
[0, 1]
```

如果按照原来的写法：

```python
[cal[nums[0]], cal[nums[1]]]
```

实际上就是：

```python
[cal[3], cal[3]]
```

最后得到：

```python
[1, 1]
```

显然错了。


---
# 四、问题其实不是“怎么存下标”，而是“哈希表到底要帮我解决什么？”

做到这里我才发现：

我虽然用了哈希表，但实际上并没有利用哈希表减少搜索。

我还是在做：

```text
nums[i] 和 nums[j] 一个个尝试
```

也就是说：

```text
先暴力找到两个数字
↓
再用哈希表找下标
```

那这个哈希表根本没有解决真正耗时的地方。

真正的问题应该换一个方向：

> **能不能不要一个个找第二个数字？**


---
# 五、不要问“它要和谁相加”，而是问“它还差多少”

比如：

```python
nums = [2, 7, 11, 15]
target = 9
```

现在看到：

```text
2
```

如果要满足：

```text
2 + ? = 9
```

那其实根本不用一个个试。

直接算：

```text
? = 9 - 2 = 7
```

也就是说：

> 当前数字是 `2`，我真正需要寻找的是 `7`。

所以可以写成：

```python
need = target - nums[i]
```

然后直接问哈希表：

```text
need 以前出现过吗？
```

这才是哈希表真正适合做的事情。


---
# 六、用 nums = [2, 7, 11, 15] 走一遍

一开始：

```python
cal = {}
```

---
## 第一次

当前数字：

```text
2
```

目标：

```text
9
```

还差：

```text
need = 9 - 2 = 7
```

现在哈希表：

```python
{}
```

里面没有：

```text
7
```

说明暂时还找不到答案。

那就先把当前数字和下标保存起来：

```python
cal[2] = 0
```

现在：

```python
cal = {
    2: 0
}
```

---
## 第二次

当前数字：

```text
7
```

还差：

```text
need = 9 - 7
     = 2
```

这一次去哈希表里找：

```text
2
```

发现：

```python
2 in cal
```

而且：

```python
cal[2] = 0
```

当前：

```python
i = 1
```

所以答案直接就是：

```python
[0, 1]
```

不需要再继续往后找了。


---
# 七、完整代码

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:

        cal = {}

        for i in range(len(nums)):

            need = target - nums[i]

            if need in cal:
                return [cal[need], i]

            cal[nums[i]] = i
```

---
# 八、为什么一定要“先查，再存”？

这里有一个很容易忽略的细节。

代码顺序是：

```python
if need in cal:
    return [cal[need], i]

cal[nums[i]] = i
```

也就是：

```text
先找
↓
再存当前数字
```

为什么？

还是看：

```python
nums = [3, 3]
target = 6
```

---
## 第一次

```text
当前数字 = 3
need = 3
```

这时候：

```python
cal = {}
```

里面没有 `3`。

所以：

```python
cal[3] = 0
```

现在：

```python
{
    3: 0
}
```

---
## 第二次

又遇到：

```text
3
```

现在：

```text
need = 3
```

去字典里找：

```python
3 in cal
```

成立。

而：

```python
cal[3] = 0
```

当前：

```python
i = 1
```

所以：

```python
return [0, 1]
```

刚好正确。

这样两个 `3` 是两个不同位置上的元素。


---
# 九、这题真正的思路变化

我一开始的想法其实是：

```text
先把数字和下标存进哈希表
↓
再找哪两个数字加起来等于 target
↓
找到以后通过哈希表返回下标
```

后来才发现：

> 这其实还是暴力搜索，只是最后多绕了一次哈希表。

真正利用哈希表以后，思路变成：

```text
遍历当前数字
↓
计算：

need = target - 当前数字

↓
问哈希表：

need 以前出现过吗？

↓
出现过
→ 直接返回两个下标

没出现
→ 把当前数字和下标存起来
```

也就是从：

```text
“当前数字要和谁相加？”
```

变成：

```text
“当前数字还缺谁？”
```

这个转换其实才是这题最关键的地方。


---
# 十、为什么这样会更快？

暴力解法需要：

```text
第一个数字和后面的数字一个个比
第二个数字继续和后面的数字一个个比
……
```

所以时间复杂度是：

```text
O(n²)
```

而哈希表方法只需要遍历一次数组：

```text
每个数字只处理一次
```

哈希表查找：

```python
need in cal
```

平均可以看作：

```text
O(1)
```

所以整体时间复杂度：

```text
O(n)
```

空间复杂度：

```text
O(n)
```

因为最坏情况下，需要把很多数字保存进哈希表。


---
# 十一、最后总结

这题让我比较明显地感觉到：

> **“用了哈希表”和“真正利用哈希表优化搜索”是两回事。**

我最开始确实创建了：

```python
cal = {}
```

但核心搜索仍然是：

```text
两层循环
```

所以算法本身并没有被优化。

真正的变化是把问题从：

```text
我要找哪两个数字相加等于 target？
```

换成：

```text
我现在已经有一个数字了，
另一个数字应该是多少？
```

于是：

```python
need = target - nums[i]
```

算出来以后，直接交给哈希表去查。

这时候哈希表才真正发挥了作用。