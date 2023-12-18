[toc]

# 剑指 Offer

## 第一版

[「剑指 Offer」 - 学习计划 - 力扣（LeetCode）全球极客挚爱的技术成长平台](https://leetcode.cn/study-plan/lcof/?progress=x9etpc0g)

*简单: 38 中等: 31 困难: 6*

### D1. 栈与队列(简单)

#### [剑指 Offer 09. 用两个栈实现队列](https://leetcode.cn/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/)

> 难度简单735
>
> 用两个栈实现一个队列。队列的声明如下，请实现它的两个函数 `appendTail` 和 `deleteHead` ，分别完成在队列尾部插入整数和在队列头部删除整数的功能。(若队列中没有元素，`deleteHead` 操作返回 -1 )
>
> 示例 1：
>
> ```
> 输入：
> ["CQueue","appendTail","deleteHead","deleteHead","deleteHead"]
> [[],[3],[],[],[]]
> 输出：[null,null,3,-1,-1]
> ```

##### 分析

将一个栈当作`输入栈`，用于压入 `appendTail` 传入的数据；另一个栈当作`输出栈`，用于 `deleteHead` 操作。

每次 `deleteHead` 时，**若`输出栈`为空则将`输入栈`的全部数据依次弹出并压入`输出栈`，这样`输出栈`从栈顶往栈底的顺序就是队列从队首往队尾的顺序。**

```python
class CQueue:
    """
    Python中没有栈结构, 这里用List模拟栈,此时:
    + append为入栈(压入栈顶)操作;
    + pop为出栈(弹出栈顶)操作
    """
    def __init__(self):
        self.input_stack = []
        self.output_stack = []

    def appendTail(self, value: int) -> None:
        self.input_stack.append(value)

    def deleteHead(self) -> int:
        if not self.output_stack:
            while self.input_stack:
                self.output_stack.append(self.input_stack.pop())
        if self.output_stack:
            return self.output_stack.pop()
        else:
            return -1
```

#### [剑指 Offer 30. 包含min函数的栈](https://leetcode.cn/problems/bao-han-minhan-shu-de-zhan-lcof/)

> 难度简单489
>
> 定义`栈`的数据结构，请在该类型中实现一个能够得到栈的最小元素的 min 函数在该栈中，调用 min、push 及 pop 的时间复杂度都是 O(1)。
>
>  **示例:**
>
> ```
> MinStack minStack = new MinStack();
> minStack.push(-2);
> minStack.push(0);
> minStack.push(-3);
> minStack.min();   --> 返回 -3.
> minStack.pop();
> minStack.top();      --> 返回 0.
> minStack.min();   --> 返回 -2.
> ```

##### 分析

我们只需要设计一个数据结构，使得*每个栈内元素*与*其相应的最小值*保持一一对应。因此我们可以使用一个**辅助栈**，与元素栈同步插入与删除，用于**存储与每个元素对应的最小值**。

+ 为了比较方便, 初始化时先在记录最小值的`辅助栈`中放入一个极大值;
+ 当一个元素要入栈时，**先取当前辅助栈的栈顶存储的最小值，与当前元素比较得出最小值**，将这个最小值插入辅助栈中；

+ 当一个元素要出栈时，我们把辅助栈的栈顶元素也一并弹出；

+ 在任意一个时刻，栈内元素的最小值就存储在辅助栈的栈顶元素中。

```python
class MinStack:
    def __init__(self):
        self.value_stack = []
        self.min_stack = [float("inf")]

    def push(self, x: int) -> None:
        curr_min = min(self.min_stack[-1], x)
        self.value_stack.append(x)
        self.min_stack.append(curr_min)

    def pop(self) -> None:
        self.value_stack.pop()
        self.min_stack.pop()

    def top(self) -> int:
        return self.value_stack[-1]

    def min(self) -> int:
        return self.min_stack[-1]
```

### D2. 链表(简单)

#### [剑指 Offer 06. 从尾到头打印链表](https://leetcode.cn/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/)

难度简单413

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

**示例 1：**

```
输入：head = [1,3,2]
输出：[2,3,1]
```

##### 分析

###### 解法一: 原地翻转链表

先原地翻转一个链表, 然后遍历翻转后的链表即为之前的逆序. **注意这个解法修改了链表, 可能不符合要求**. (翻转链表的解法见 [剑指 Offer 24. 反转链表](https://leetcode.cn/problems/fan-zhuan-lian-biao-lcof/))

###### 解法二: 辅助栈法

利用栈的`先入后出`的性质, 使用一个辅助栈完成. 先遍历一遍链表, 依次压入栈. 然后依次弹出栈顶, 并保存到数组, 此时即为原链表的逆序.

```python
class Solution:
    def reversePrint(self, head: ListNode) -> List[int]:
        if not head:
            return []
        stack = []
        p = head
        # 先遍历一遍链表, 依次压入栈
        while p:
            stack.append(p.val)
            p = p.next
        res = []
        # 依次弹出栈顶, 并保存到数组
        while stack:
            res.append(stack.pop())
        return res
```

###### 解法三: 递归回溯

先走至链表末端，**回溯**时依次将节点值加入列表 ，这样就可以实现链表值的倒序输出。

```python
class Solution:
    def reversePrint(self, head: ListNode) -> List[int]:
        res = []

        def helper(head: ListNode):
            if head.next:
                helper(head.next)
            res.append(head.val)
        
        if not head:
            return []
        helper(head)
        return res
```

#### [剑指 Offer 24. 反转链表](https://leetcode.cn/problems/fan-zhuan-lian-biao-lcof/)

> 难度简单570
>
> 定义一个函数，输入一个链表的头节点，反转该链表并输出反转后链表的头节点。
>
> **示例:**
>
> ```
> 输入: 1->2->3->4->5->NULL
> 输出: 5->4->3->2->1->NULL
> ```

##### 分析

###### 解法一: 迭代法

利用两个指针实现链表翻转

```python
class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        if not head:
            return head
        cur, pre = head, None
        while cur:
            cur.next, pre, cur = pre, cur, cur.next
        return pre
```

解法二: 递归回溯

使用递归法遍历链表，当越过尾节点后终止递归，在回溯时修改各节点的 `next` 引用指向.

```python
class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        if not head:
            return head

        def helper(curr, prev):
            if not curr:
                return prev
            res = helper(curr.next, curr)
            curr.next = prev
            return res

        return helper(head, None)
```

#### [剑指 Offer 35. 复杂链表的复制](https://leetcode.cn/problems/fu-za-lian-biao-de-fu-zhi-lcof/)

> 难度中等718
>
> 请实现 `copyRandomList` 函数，复制一个复杂链表。在复杂链表中，每个节点除了有一个 `next` 指针指向下一个节点，还有一个 `random` 指针指向链表中的任意节点或者 `null`。
>
>  
>
> **示例 1：**
>
> <img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230422221907.png" alt="img" style="zoom: 40%;" />
>
> ```
> 输入：head = [[7,null],[13,0],[11,4],[10,2],[1,0]]
> 输出：[[7,null],[13,0],[11,4],[10,2],[1,0]]
> ```



##### 分析

如果是普通链表，我们可以直接按照遍历的顺序创建链表节点。而本题中因为随机指针的存在，当我们拷贝节点时，「当前节点的随机指针指向的节点」可能还没创建，因此我们需要变换思路。

###### 解法一: 借助哈希表

首先构建一个表示`原始节点`和`新的节点`之间映射关系的哈希表`nodes_map`. 沿着`next`遍历一遍原始链表, 同时创建`新节点`, 令新节点`next`和`random`为空, 并保存入哈希表.

<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230422223228.png" alt="image-20230422223228437" style="zoom: 33%;" />

再沿着`next`遍历一遍原始链表, 指针为`cur`, 此时新节点为`nodes_map[cur]`, 则令`nodes_map[cur].next = nodes_map.get(cur.next, None)`, `nodes_map[cur].random = nodes_map.get(cur.random, None)`即可完成复制. 

<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230422223844.png" alt="image-20230422223844872" style="zoom:33%;" />

此时新的链表的头结点为`nodes_map[head]`

```python
class Solution:
    def copyRandomList(self, head: 'Node') -> 'Node':
        if not head:
            return None
        cur = head
        nodes_map = {}
        # 首先构建一个表示`原始节点`和`新的节点`之间映射关系的哈希表
        while cur:
            new_node = Node(cur.val)
            nodes_map[cur] = new_node
            cur = cur.next
        cur = head
        # 利用哈希表 重建指针
        while cur:
            nodes_map[cur].next = nodes_map.get(cur.next, None)
            nodes_map[cur].random = nodes_map.get(cur.random, None)
            cur = cur.next
        return nodes_map[head]
```

时间复杂度为: $O(n)$ (两次遍历)

空间复杂度为: $O(n)$

###### 解法二: 拼接+拆分

+ 先构建一个`原节点1 -> 新节点1 -> 原节点2 -> 新节点2 -> ……`的拼接链表, 此时`新节点1`的`next`指向`原节点2`.

+ 然后用`cur`指针遍历这个拼接链表, 则有`cur.next.random = cur.random.next`, `cur`每次前移两个节点. 遍历结束后可以重建新节点的`random`指针.
+ 用两个指针拆分拼接链表, 重置两个链表节点的`next`指针.

```python
class Solution:
    def copyRandomList(self, head: 'Node') -> 'Node':
        if not head:
            return None
        # 在原链表基础上创建一个拼接链表: 
        # 原节点1 -> 新节点1 -> 原节点2 -> 新节点2 -> ……
        cur = head
        while cur:
            new_node = Node(cur.val)
            new_node.next = cur.next
            cur.next = new_node
            cur = new_node.next
        # 重置拼接链表中新节点的random指针
        cur = head
        while cur:
            # random指针可能指向None
            if cur.random:
                cur.next.random = cur.random.next
            cur = cur.next.next
        # 拆分拼接链表
        new_head = head.next
        cur = head
        while cur.next:
            temp = cur.next
            cur.next = cur.next.next
            cur = temp
        return new_head
```

时间复杂度为: $O(n)$ (三次遍历)

空间复杂度为: $O(1)$



### D3. 字符串(简单)

#### [剑指 Offer 05. 替换空格](https://leetcode.cn/problems/ti-huan-kong-ge-lcof/)

> 难度简单465
>
> 请实现一个函数，把字符串 `s` 中的每个空格替换成"%20"。
>
>  **示例 1：**
>
> ```
> 输入：s = "We are happy."
> 输出："We%20are%20happy."
> ```

##### 分析

在 Python 和 Java 等语言中，字符串都被设计成「不可变」的类型，即无法直接修改字符串的某一位字符，需要新建一个字符串实现。

```python
class Solution:
    def replaceSpace(self, s: str) -> str:
        chars = []
        for ch in s:
            if ch == " ":
                chars.append("%20")
            else:
                chars.append(ch)
        return "".join(chars)
```

在 C++ 语言中， string 被设计成「可变」的类型（[参考资料](https://leetcode.cn/link/?target=https://stackoverflow.com/questions/28442719/are-c-strings-mutable-unlike-java-strings)），因此可以在不新建字符串的情况下实现原地修改

```cpp
class Solution {
public:
    string replaceSpace(string s) {
        int count = 0, len = s.size();
        // 统计空格数量
        for (char c : s) {
            if (c == ' ') count++;
        }
        // 修改 s 长度
        s.resize(len + 2 * count);
        // 倒序遍历修改
        for(int i = len - 1, j = s.size() - 1; i < j; i--, j--) {
            if (s[i] != ' ')
                s[j] = s[i];
            else {
                s[j - 2] = '%';
                s[j - 1] = '2';
                s[j] = '0';
                j -= 2;
            }
        }
        return s;
    }
};
```

#### [剑指 Offer 58 - II. 左旋转字符串](https://leetcode.cn/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/)

> 难度简单389
>
> 字符串的左旋转操作是把字符串前面的若干个字符转移到字符串的尾部。请定义一个函数实现字符串左旋转操作的功能。比如，输入字符串"abcdefg"和数字2，该函数将返回左旋转两位得到的结果"cdefgab"。
>
>  **示例 1：**
>
> ```
> 输入: s = "abcdefg", k = 2
> 输出: "cdefgab"
> ```

##### 分析

> [Efficient String Concatenation in Python](https://waymoot.org/home/python_string/)

```python
class Solution:
    def reverseLeftWords(self, s: str, n: int) -> str:
        # >>> 遍历拼接解法:
        # chars = []
        # for i in range(n, len(s), 1):
        #     chars.append(s[i])
        # for i in range(n):
        #     chars.append(s[i])
        # return "".join(chars)
        
        # >>> 切片解法:
        return s[n:]+s[:n]
```

### D4. 查找算法(简单)

#### [剑指 Offer 03. 数组中重复的数字](https://leetcode.cn/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/)

> 难度简单1155
>
> 找出数组中重复的数字。
>
> 在一个长度为 `n` 的数组 `nums` 里的所有数字都在 `0～n-1` 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请**找出数组中任意一个重复的数字**。
>
> **示例 1：**
>
> ```
> 输入：
> [2, 3, 1, 0, 2, 5, 3]
> 输出：2 或 3 
> ```

##### 分析

###### 解法一: 借助哈希表

遍历时判断当前元素是否在哈希表中, 如果存在则直接返回, 否则则将当前元素保存入哈希表

```python
class Solution:
    def findRepeatNumber(self, nums: List[int]) -> int:
        cache = set()
        for x in nums:
            if x in cache:
                return x
            cache.add(x)
        return -1
```

时间复杂度: $O(n)$

空间复杂度: $O(n)$

> `set`或`dict`的`in`操作平均时间复杂度为 $O(1)$

###### 解法二: 原地交换

本题还有一个条件没有使用, 即: **在一个长度为`n`的数组`nums`里的所有数字都在`0 ~ n-1`的范围内**. 这说明, 如果有重复值, 则数组的索引和取值是`一对多`的关系.

因此可以通过遍历数组并交换, 使得数组的索引和取值一一对应(`nums[i]==i`), 此时数组起到类似映射关系的作用.

在遍历数组时, 如果遇到元素`nums[i]=x`, 则交换`nums[i]`和`nums[x]`(即交换`nums[i]`和`nums[[nums[i]]]`), 继续遍历数组, 如果又遇到`nums[j]=x`, 则此时一定有`nums[x] = nums[nums[j]] = x = nums[j]`, 即`nums[nums[j]] == nums[j]`, 此时即找到一对重复值.

 因此, 用索引`i`遍历数组:



```python
class Solution:
    def findRepeatNumber(self, nums: [int]) -> int:
        i = 0
        while i < len(nums):
            if nums[i] == i:
                i += 1
                continue
            if nums[nums[i]] == nums[i]: 
                return nums[i]
            nums[nums[i]], nums[i] = nums[i], nums[nums[i]]
        return -1
```



#### [剑指 Offer 53 - I. 在排序数组中查找数字 I](https://leetcode.cn/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/)

> 难度简单415
>
> 统计一个数字在**排序**数组中出现的**次数**。
>
> **示例 1:**
>
> ```
> 输入: nums = [5,7,7,8,8,10], target = 8
> 输出: 2
> ```

##### 分析

###### 解法一: 二分查找

现查找右边界, 再查找左边界, 然后相减得到同值序列的长度

```python
import math
class Solution:
    def binary_count(self, nums, target) -> int:
        # 查找右边界
        left = 0
        right = len(nums) - 1
        while left < right:
            mid = math.ceil((right+left)/2)
            if nums[mid] == target:
                # 注意
                left = mid
            elif nums[mid] < target:
                left = mid + 1
            else:
                right = mid -1
        if nums[right] == target:
            # 找到了右边界
            right_boundary = right
        else:
            # 没有找到右边界, 说明目标不存在(次数为0)
            return 0
        
        # 查找左边界
        left = 0
        # 不需要重置right
        while left < right:
            mid = math.floor((right+left)/2)
            if nums[mid] == target:
                right = mid
            elif nums[mid] < target:
                left = mid + 1
            else:
                right = mid - 1
        # 能找到右边界, 说明左边界一定存在
        return right_boundary - left + 1

    def search(self, nums: List[int], target: int) -> int:
        if not nums:
            return 0
        return self.binary_count(nums, target)
```

> 有待优化

时间复杂度: $O(\log{n})$

空间复杂度: $O(1)$



#### [剑指 Offer 53 - II. 0～n-1中缺失的数字](https://leetcode.cn/problems/que-shi-de-shu-zi-lcof/)

> 难度简单373
>
> 一个长度为`n-1`的**递增排序**数组中的所有数字都是唯一的，并且每个数字都在范围`0～n-1`之内。在范围`0～n-1`内的`n`个数字中**有且只有一个**数字不在该数组中，请找出这个数字。
>
>  **示例 1:**
>
> ```
> 输入: [0,1,3]
> 输出: 2
> ```



##### 分析

###### 解法一: 二分查找

由于数组为升序, 且长度为`n-1`, 取值范围为`0~n-1`. 因此数组可以划分两个部分, 左边部分索引和取值一一对应; 右边部分索引和取值不相等, 且缺失值就是右边部分的首个元素的对应索引.

例如: `[0, 1, 2, 4, 5, 6]`, 左边部分为`[0, 1, 2]`, 索引从`0`到`2`; 右边部分为 `[4, 5, 6]`, 索引从`3`到`5`, 缺失值为右边部分首个索引`3`

因此我们考虑使用`二分法`来查找右边部分的首个元素.

 ```python
 class Solution:
     def missingNumber(self, nums: List[int]) -> int:
         left = 0
         right = len(nums) - 1
         while left <= right:
             mid = (right+left) // 2
             if nums[mid] == mid: # 索引和取值一一对应, 属于左边部分
                 left = mid + 1
             else:
                 # 索引和取值不对应,属于右边部分
                 right = mid - 1
         return left
 ```

### D5. 查找算法(中等)

#### [剑指 Offer 04. 二维数组中的查找](https://leetcode.cn/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/)

> 难度中等937
>
> 在一个 `n * m` 的二维数组中，每一行都按照从左到右 **非递减** 的顺序排序，每一列都按照从上到下 **非递减** 的顺序排序。请完成一个高效的函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。
>
>  **示例:**
>
> ```
> 现有矩阵 matrix 如下：
> [
>   [1,   4,  7, 11, 15],
>   [2,   5,  8, 12, 19],
>   [3,   6,  9, 16, 22],
>   [10, 13, 14, 17, 24],
>   [18, 21, 23, 26, 30]
> ]
> 给定 target = `5`，返回 `true`。
> 给定 target = `20`，返回 `false`。
> ```



##### 分析

若使用暴力法遍历矩阵, 则时间复杂度为 $O(NM)$ 。显然不是最优解法。

<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230423110844.png" alt="image-20230423110837700" style="zoom:50%;" />

如果按照图上选择右上角作为根节点的方式, 此时根节点为`(i,j)=matrix[0][M-1]`, 左子树为`(i,j-1)`, 右子树为`(i+1,j)`

> 也可以选择左下角作为根节点, 向上搜索.

```python
class Solution:
    def findNumberIn2DArray(self, matrix: List[List[int]], target: int) -> bool:
        if not matrix:
            return False
        N = len(matrix)
        M = len(matrix[0])
        i = 0
        j = M -1
        while i <= N-1 and j >= 0:
            if matrix[i][j] == target:
                return True # 坐标即为(i,j)
            elif matrix[i][j] > target:
                j -= 1
            else:
                i += 1
        return False # 没找到 
```

#### [剑指 Offer 11. 旋转数组的最小数字](https://leetcode.cn/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/)

> 难度简单807
>
> 把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。
>
> 给你一个可能存在 **重复** 元素值的数组 `numbers` ，它原来是一个**升序**排列的数组，并按上述情形进行了一次旋转。请返回旋转数组的**最小元素**。例如，数组 `[3,4,5,1,2]` 为 `[1,2,3,4,5]` 的一次旋转，该数组的最小值为 1。 
>
>  **示例 1：**
>
> ```
> 输入：numbers = [3,4,5,1,2]
> 输出：1
> ```

##### 分析

###### 解法一: 二分查找

> 排序数组的查找问题首先考虑使用 **二分法** 解决，其可将 **遍历法** 的 **线性级别** 时间复杂度降低至 **对数级别** 。

<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230423113058.png" alt="image-20230423113057865" style="zoom:50%;" />

此时的二分查找算法如下:

+ 初始化`left`和`right`分别指向数组两端;
+ 循环二分查找(`left < right`):
  + 取中点 `mid=(right+left)//2`, 此时有 `left < mid < right`;
  + 如果 `nums[mid] > nums[right]`, 则此时`[left, mid]`必为左升序数组的一部分, 旋转点`x`必然在`[mid+1, right]`中, 此时令`left = mid + 1`;
  + 如果 `nums[mid] < nums[left]`, 则此时`[mid, right]`必为右升序数组的一部分, 旋转点`x`必然在`[left, mid]`中, 此时令`right = mid`
  + 如果`nums[mid] == nums[right]`, 此时无法判断旋转点的位置, 令`right=right-1`缩小范围

+ 当`left >= right`时退出循环, 此时旋转点即为`nums[left]`

```python
class Solution:
    def minArray(self, numbers: List[int]) -> int:
        left = 0
        right = len(numbers) - 1
        while left < right:
            mid = (right+left) // 2
            if numbers[mid] > numbers[right]:
                left = mid + 1
            elif numbers[mid] < numbers[left]:
                right = mid
            elif numbers[mid] == numbers[right]:
                # 例如 [3,3,1,3]
                # 全部相等时退化为线性搜索
                right -= 1
            else:
                # nums[left] <= nums[mid] < nums[right]
                # 整体有序, 直接返回左边界
                return numbers[left]
        return numbers[left]
```

#### [剑指 Offer 50. 第一个只出现一次的字符](https://leetcode.cn/problems/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-lcof/)

> 难度简单323
>
> 在字符串 `s` 中找出第一个只出现一次的字符。如果没有，返回一个单空格。 `s` 只包含小写字母。
>
> **示例 1:**
>
> ```
> 输入：s = "abaccdeff"
> 输出：'b'
> ```

##### 分析

先遍历一遍字符串, 将每个字符出现的次数保存在哈希表中, 由于Python3字典默认有序, 因此第一个次数等于1的字符就是本题的答案

```python
class Solution:
    def firstUniqChar(self, s: str) -> str:
        if not s:
            return " "
        cache = {}
        for ch in s:
            # 这里也可以考虑将出现大于1次的key从cache删除
            # Python set/dict 的查找/插入/删除的 平均复杂度都为 $O(1)$
            # 最坏复杂度为 $O(n)$
            cache[ch] = cache.get(ch, 0) + 1
        for k,v in cache.items():
            if v == 1:
                return k
        return " "
```

#### [136. 只出现一次的数字](https://leetcode.cn/problems/single-number/)

> 难度简单2850
>
> 给你一个 **非空** 整数数组 `nums` ，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。
>
> 你必须设计并实现线性时间复杂度的算法来解决此问题，且该算法只使用常量额外空间。
>
>  **示例 1 ：**
>
> ```
> 输入：nums = [2,2,1]
> 输出：1
> ```

##### 分析

本题和上一题 [剑指 Offer 50. 第一个只出现一次的字符](https://leetcode.cn/problems/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-lcof/) 非常相似, 但是有几处不同:

+ 本题是在一个整数数组中找到唯一的只出现一次的**数字**;
+ 本题要求$O(n)$时间复杂度和$O(1)$的空间复杂度;

上一题的解法虽然也可以求解本题, 但是无法满足对空间复杂度的要求, 由于本题是整数, 因此可以使用**位运算**解决.

考虑异或(`x^y`)运算, 异或运算有三个性质:

+ 任何数和`0`做异或, 其结果仍是原来的数, 即: $a\oplus{0}=a$ ;
+ 任何数和其自身做异或, 其结果都是`0`, 即 $a\oplus{a}=0$
+ 异或运算满足`交换律`和`结合律`, 即 $a\oplus{b}\oplus{a}=b\oplus{a}\oplus{a}=b\oplus{(a\oplus{a})}=b\oplus{0}=b$

现在假设数组中有`2m+1`个数字, 其中`m`个数字出现两次. 令 $a_1, ..., a_m$为出现了两次的`m`个数字, $a_{m+1}$为仅出现一次的数字. 根据上述性质3, 数组中所有数字做异或, 其结果等于:
$$
(a_1\oplus{a_1})\oplus{...}\oplus{(a_m\oplus{a_m})}\oplus{a_{m+1}}\\
=0\oplus{...}\oplus{0}\oplus{a_{m+1}}\\
=0\oplus{a_{m+1}} = a_{m+1}
$$
 因此, 数组中的所有数字做异或运算, 其结果即为数组中仅出现一次的数字

```python
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        # return reduce(lambda x, y: x ^ y, nums)
        xor_res = 0
        for x in nums:
            xor_res = xor_res ^ x
        return xor_res
```



### D6. 搜索与回溯算法(简单)

#### [剑指 Offer 32 - I. 从上到下打印二叉树](https://leetcode.cn/problems/cong-shang-dao-xia-da-yin-er-cha-shu-lcof/)

> 难度中等272
>
> 从上到下打印出二叉树的每个节点，同一层的节点按照从左到右的顺序打印。
>
>  例如:
> 给定二叉树: `[3,9,20,null,null,15,7]`,
>
> ```
>     3
>    / \
>   9  20
>     /  \
>    15   7
> ```
>
> 返回：
>
> ```
> [3,9,20,15,7]
> ```

##### 分析

考察二叉树的`层序遍历`

```python
class Solution:
    def levelOrder(self, root: TreeNode) -> List[int]:
        if not root:
            return []
        queue = [root]
        res = []
        while queue:
            level_nodes = []
            for node in queue:
                res.append(node.val)
                if node.left:
                    level_nodes.append(node.left)
                if node.right:
                    level_nodes.append(node.right)
            queue = level_nodes
        return res
```

#### [剑指 Offer 32 - II. 从上到下打印二叉树 II](https://leetcode.cn/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/)

> 难度简单288
>
> 从上到下按层打印二叉树，同一层的节点按从左到右的顺序打印，每一层打印到一行。
>
>  例如:
> 给定二叉树: `[3,9,20,null,null,15,7]`,
>
> ```
>     3
>    / \
>   9  20
>     /  \
>    15   7
> ```
>
> 返回其层次遍历结果：
>
> ```
> [
>   [3],
>   [9,20],
>   [15,7]
> ]
> ```

##### 分析

考察二叉树的`层序遍历`, 只不过这里每一层保存在一个list中

```python
class Solution:
    def levelOrder(self, root: TreeNode) -> List[List[int]]:
        if not root:
            return []
        queue = [root]
        res = []
        while queue:
            level_nodes = []
            level_res = []
            for node in queue:
                level_res.append(node.val)
                if node.left:
                    level_nodes.append(node.left)
                if node.right:
                    level_nodes.append(node.right)
            res.append(level_res)
            queue = level_nodes
        return res
```

#### [剑指 Offer 32 - III. 从上到下打印二叉树 III](https://leetcode.cn/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof/)

> 难度中等284
>
> 请实现一个函数按照之字形顺序打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右到左的顺序打印，第三行再按照从左到右的顺序打印，其他行以此类推。
>
>  例如:
> 给定二叉树: `[3,9,20,null,null,15,7]`,
>
> ```
>     3
>    / \
>   9  20
>     /  \
>    15   7
> ```
>
> 返回其层次遍历结果：
>
> ```
> [
>   [3],
>   [20,9],
>   [15,7]
> ]
> ```

##### 分析

和上一题基本一致, 只不过是**之字形遍历**

```python
class Solution:
    def levelOrder(self, root: TreeNode) -> List[List[int]]:
        if not root:
            return []
        nodes_queue = [root]
        res = []
        i = 0
        while nodes_queue:
            next_level = []
            res_level = []
            for node in nodes_queue:
                res_level.append(node.val)
                if node.left:
                    next_level.append(node.left)
                if node.right:
                    next_level.append(node.right)
            nodes_queue = next_level
            if i % 2 == 0:
                res.append(res_level)
            else:
                res.append(res_level[::-1])
            i += 1
        return res
```

### D7. 搜索与回溯算法(简单)

#### [剑指 Offer 26. 树的子结构](https://leetcode.cn/problems/shu-de-zi-jie-gou-lcof/)

>难度中等740
>
>输入两棵二叉树A和B，判断B是不是A的子结构。(约定空树不是任意一个树的子结构)
>
>B是A的子结构， 即 A中有出现和B相同的结构和节点值。
>
>```
>例如:
>给定的树 A:
>     3
>    / \
>   4   5
>  / \
> 1   2
>给定的树 B：
>   4 
>  /
> 1
>返回 true，因为 B 与 A 的一个子树拥有相同的结构和节点值。
>```
>
>**示例 1：**
>
>```
>输入：A = [1,2,3], B = [3,1]
>输出：false
>```

##### 分析

本题和 [572. 另一棵树的子树](https://leetcode.cn/problems/subtree-of-another-tree/) 很类似, 但是这里判断的是`B是不是A的子结构`, 而不是`B是不是A的子树`, 注意这两个有很大区别.

例如, `A=[10, 12, 6, 8, 3, 11], B=[10, 12, 6, 8]`, 此时`B`是`A`的**子结构**, 但不是`A`的**子树**!!

这里定义一个判断子结构辅助方法, 同时遍历两个树`a`和`b`, 判断`b`是否是`a`的同一个根节点的子结构. 然后遍历`A`中的所有节点, 依次调用这个辅助方法, 判断以这个节点为根的树是否和`B`是同根, 且包含`B`(作为子结构).

```python
class Solution:
    def helper(self, a: TreeNode, b: TreeNode) -> bool:
        """
        遍历两棵树, 判断b是否是a的子结构(注意不是子树)
        """
        if not b:
            # 空节点视为是子结构, 注意这不违背题目要求
            return True
        elif not a:
            # a节点为空, 但是b不为空, 此时b必然不可能是a的子结构
            return False
        if a.val != b.val:
            # 节点取值不同, 必然不是子结构
            return False
        else:
            # 节点值相同, 进一步检查左右子树
            return self.helper(a.left, b.left) and self.helper(a.right, b.right)

    def isSubStructure(self, A: TreeNode, B: TreeNode) -> bool:
        if not B:
            # 约定空树不是任意一个树的子结构
            return False
        # 遍历A树中的每个节点, 将其作为根节点,
        # 判断其表示的树是否包含B
        def dfs(a: TreeNode):
            if not a:
                return False
            if self.helper(a, B):
                return True
            else:
                return dfs(a.left) or dfs(a.right)
        return dfs(A)
```

#### [剑指 Offer 27. 二叉树的镜像](https://leetcode.cn/problems/er-cha-shu-de-jing-xiang-lcof/)

> 难度简单352
>
> 请完成一个函数，输入一个二叉树，该函数输出它的镜像。
>
> ```
> 例如输入：
>      4
>    /   \
>   2     7
>  / \   / \
> 1   3 6   9
> 镜像输出：
>      4
>    /   \
>   7     2
>  / \   / \
> 9   6 3   1
> ```
>
>  **示例 1：**
>
> ```
> 输入：root = [4,2,7,1,3,6,9]
> 输出：[4,7,2,9,6,3,1]
> ```

##### 分析

本题和 [226. 翻转二叉树](https://leetcode.cn/problems/invert-binary-tree/) 相同, 就是翻转一颗二叉树.

```python
class Solution:
    def mirrorTree(self, root: TreeNode) -> TreeNode:
        if not root:
            return None
        # 交换左右子树
        root.left, root.right = root.right, root.left
        # 继续递归遍历
        self.mirrorTree(root.left)
        self.mirrorTree(root.right)
        return root
```

#### [剑指 Offer 28. 对称的二叉树](https://leetcode.cn/problems/dui-cheng-de-er-cha-shu-lcof/)

> 难度简单442
>
> 请实现一个函数，用来判断一棵二叉树是不是对称的。如果一棵二叉树和它的镜像一样，那么它是对称的。

##### 分析

本题和 [101. 对称二叉树](https://leetcode.cn/problems/symmetric-tree/) 一致

同时遍历左右子树, 判断左右子树是否对称

```python
class Solution:
    def isSymmetric(self, root: TreeNode) -> bool:
        if not root:
            # 空节点视为是对称的
            return True

        def helper(a: TreeNode, b: TreeNode) -> bool:
            if not a and not b:
                return True
            elif not a or not b:
                return False
            if a.val == b.val:
                return helper(a.left, b.right) and helper(a.right, b.left)
            else:
                return False
        
        return helper(root.left, root.right)
```

### D8. 动态规划(简单)

#### [剑指 Offer 10- I. 斐波那契数列](https://leetcode.cn/problems/fei-bo-na-qi-shu-lie-lcof/)

> 难度简单474
>
> 写一个函数，输入 `n` ，求斐波那契（Fibonacci）数列的第 `n` 项（即 `F(N)`）。斐波那契数列的定义如下：
>
> ```
> F(0) = 0,   F(1) = 1
> F(N) = F(N - 1) + F(N - 2), 其中 N > 1.
> ```
>
> **答案需要取模 1e9+7（1000000007）**. 如计算初始结果为：1000000008，请返回 1。

##### 分析(动态规划)

`动态规划`解法如下:

```python
class Solution:
    def fib(self, n: int) -> int:
        if n <= 1:
            return n
        first = 0
        second = 1
        for i in range(2, n+1, 1):
            third = (first + second) % 1000000007
            first, second = second, third
        return third
```

#### [剑指 Offer 10- II. 青蛙跳台阶问题](https://leetcode.cn/problems/qing-wa-tiao-tai-jie-wen-ti-lcof/)

> 难度简单385
>
> 一只青蛙一次可以跳上1级台阶，也可以跳上2级台阶。求该青蛙跳上一个 `n` 级的台阶总共有多少种跳法。
>
> 答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。
>
> **示例 1：**
>
> ```
> 输入：n = 2
> 输出：2
> ```

##### 分析(动态规划)

本题和 [70. 爬楼梯](https://leetcode.cn/problems/climbing-stairs/) 一致. 令`dp[n]`为爬到`n`级的方法. 要跳上`n`级, 可以从`n-1`级跳, 也可以从`n-2`级跳. 则此时有:
$$
dp[0] = 1,\quad dp[1] = 1,\quad dp[2]=2,\quad dp[n]=dp[n-1]+dp[n-2]
$$
此时类似斐波那契数列

```python
class Solution:
    def numWays(self, n: int) -> int:
        if n <= 1:
            return 1
        first = 1
        second = 1
        for i in range(2, n+1, 1):
            third = (first + second) % 1000000007
            first, second = second, third
        return third
```

#### [剑指 Offer 63. 股票的最大利润](https://leetcode.cn/problems/gu-piao-de-zui-da-li-run-lcof/)

> 难度中等340
>
> 假设把某股票的价格按照时间先后顺序存储在数组中，请问买卖该股票一次可能获得的最大利润是多少
>
> 示例:
>
> ```
> 输入: [7,1,5,3,6,4]
> 输出: 5
> 解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
>      注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格。
> 
> ```

##### 分析(动态规划)

本题和 [121. 买卖股票的最佳时机](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/) 一致. 令`dp[n]`为到第`n`为止的**最低买入价格**, 则`dp[n]=min(dp[n-1], prices[n])`. 求出每天的**最低买入价格**, 即可知道每天的最大利润. 然后取`max`即为可得的最大利润

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices:
            return 0
        # 令`dp[n]`为到第`n`为止的最低买入价格
        n = len(prices)
        prev = prices[0]
        max_profit = 0
        for i in range(1, n, 1):
            curr = min(prev, prices[i])
            prev = curr
            max_profit = max(max_profit, prices[i] - curr)
        return max_profit
```



### D9. 动态规划(中等)

#### [剑指 Offer 42. 连续子数组的最大和](https://leetcode.cn/problems/lian-xu-zi-shu-zu-de-zui-da-he-lcof/)

> 难度简单692
>
> 输入一个整型数组，数组中的一个或连续多个整数组成一个子数组。求所有子数组的和的最大值。
>
> 要求时间复杂度为O(n)。
>
>  **示例1:**
>
> ```
> 输入: nums = [-2,1,-3,4,-1,2,1,-5,4]
> 输出: 6
> 解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
> ```

##### 分析(动态规划)

本题和 [53. 最大子数组和](https://leetcode.cn/problems/maximum-subarray/) 相同. 这是单个序列的最值问题, 可以用动态规划求解. 我们令`dp[n]`为以`nums[n]`为结尾的连续子数组的最大和. 容易写出递推公式如下:
$$
dp[i]=
\begin{cases}
nums[0],& \quad i=0 \\
max(nums[i], dp[i-1]+nums[i]),& \quad i>0
\end{cases}
$$

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        # 定义 dp[i]为以 nums[i]为结尾的最大连续子数组之和
        if len(nums)==1:
            return nums[0]
        dp = [0] * len(nums)
        dp[0] = nums[0]
        for i in range(1, len(nums)):
            dp[i] = max(nums[i], dp[i-1]+nums[i])
        return max(dp)
```

优化后得到

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        max_val = nums[0]
        prev = 0
        for x in nums:
            curr = max(x, prev + x)
            max_val = max(max_val, curr)
            prev = curr
        return max_val
```

#### [剑指 Offer 47. 礼物的最大价值](https://leetcode.cn/problems/li-wu-de-zui-da-jie-zhi-lcof/)

> 难度中等484
>
> 在一个 `m*n` 的棋盘的每一格都放有一个礼物，每个礼物都有一定的价值（价值大于 0）。你可以从棋盘的左上角开始拿格子里的礼物，并每次`向右`或者`向下`移动一格、直到到达棋盘的右下角。给定一个棋盘及其上面的礼物的价值，请计算你最多能拿到多少价值的礼物？

##### 分析(动态规划)

本题和 [64. 最小路径和](https://leetcode.cn/problems/minimum-path-sum/) 类似. 定义`dp[m, n]`为到达`(m,n)`格时的所能获取的**最大价值**. 由于每次只能向下或者向右移动, 因此容易得到如下递推公式:
$$
dp[m,n] = 
\begin{cases}
\sum_{j=0}^{n}{grid[0,j]} &,\quad m=0 \\
\sum_{i=0}^{m}{grid[i,0]} &,\quad n=0 \\
max\{ (dp[m-1,n]+grid[m,n]),(dp[m,n-1]+grid[m,n]) \} &,\quad m\neq{0} \and n\neq{0}
\end{cases}
$$

```python
class Solution:
    def maxValue(self, grid: List[List[int]]) -> int:
        m = len(grid)
        n = len(grid[0])
        dp = [
            [0 for _ in range(n)]
            for _ in range(m)
        ]
        dp[0][0] = grid[0][0]
        for i in range(1, m, 1):
            dp[i][0] = dp[i-1][0] + grid[i][0]
        for j in range(1, n, 1):
            dp[0][j] = dp[0][j-1] + grid[0][j]
        for i in range(1, m, 1):
            for j in range(1, n, 1):
                dp[i][j] = max(dp[i-1][j], dp[i][j-1]) + grid[i][j]
        return dp[-1][-1]
```

本题中, 我们也可以使用`grid`作为`dp`矩阵, 避免重新申请空间. 此时实现为:

```python
class Solution:
    def maxValue(self, grid: List[List[int]]) -> int:
        m = len(grid)
        n = len(grid[0])
        for i in range(1, m, 1):
            grid[i][0] += grid[i-1][0]
        for j in range(1, n, 1):
            grid[0][j] += grid[0][j-1]
        for i in range(1, m, 1):
            for j in range(1, n, 1):
                grid[i][j] += max(grid[i-1][j], grid[i][j-1])
        return grid[-1][-1]
```

### D10. 动态规划(中等)

#### [剑指 Offer 46. 把数字翻译成字符串](https://leetcode.cn/problems/ba-shu-zi-fan-yi-cheng-zi-fu-chuan-lcof/)

> 难度中等566
>
> 给定一个数字，我们按照如下规则把它翻译为字符串：0 翻译成 “a” ，1 翻译成 “b”，……，11 翻译成 “l”，……，25 翻译成 “z”。一个数字可能有多个翻译。请编程实现一个函数，用来计算一个数字有多少种不同的翻译方法。
>
>  **示例 1:**
>
> ```
> 输入: 12258
> 输出: 5
> 解释: 12258有5种不同的翻译，分别是"bccfi", "bwfi", "bczi", "mcfi"和"mzi"
> ```

##### 分析(动态规划)

本题首先要厘清翻译的过程, 我们以数字 `1402`为例, 尝试翻译这个数字, 此时可以分为两种情况:

+ 把每一位数字单独翻译, 由于`0~9`肯定是可以翻译, 这样每一位都会翻译为字符, 即 `[1,4,0,2]`翻译为`beac`;

+ 尝试组合某些连续的两位数字(这里翻译的区间在`0`到`25`之间, 因此最多同时翻译两位数字), 此时有:

  + `[14, 0, 2]`, 此时都在`0~25`之间, 可以翻译为 `oac`;
  + `[1, 40, 2]`, 此时`40`不可翻译, 不合法;
  + `[1, 4, 02]`, 此时`02`也不可翻译(前导零的两位数不符合翻译规则), 不合法;

  进而我们可以总结出翻译的规则:

  + 可以每一位单独来翻译;
  + 如果第$i-1$位和第$i$位组成的数字, 位于`10~25`之间, 则可以把这两位数字一起翻译

我们令 $f(i)$ 表示以前 $i$ 位数字组成的数( $x_1x_2...x_{i-2}x_{i-1}x_i$ )翻译的方案数, 则以 $x_{i-1}$ 结尾的翻译方案数为 $f(i-1)$, 以 $x_{i-2}$ 结尾的翻译方案为 $f(i-2)$ . 参照我们上面的翻译规则, 此时有两种情况:

+  $x_{i}$ 是一位数字, 肯定可以单独翻译, 此时的方案数等于 $f(i-1)$ 
+ 如果此时 $x_{i-1}x_{i}$ 这两位组成的数在 $[10, 25]$ 之间, 则 $x_{i-1}x_{i}$ 可以被一起翻译, 此时的方案数等于 $f(i-2)$;

故我们有如下的递推公式:
$$
f(i) = \
\begin{cases}
f(i-1),\quad& x_{i-1}x_{i} \notin [10,25] \\
f(i-1) + f(i-2),\quad& x_{i-1}x_{i} \in [10,25] 
\end{cases}
$$
考虑边界情况, $f(0)=1$, $f(1)=1$, 即 “无数字” 和 “1位数字” 的翻译方法数量均为 1

> 注意 $f(0)$ 表示的是前`0`位数字组成的数字, 实际就是一个空数字, 不存在.

根据上述定义, 容易写出如下的动态规划实现. 

> 注意在实现时, 如果数字长度为`n`, 则`dp`数组的长度应该为`n+1`(因为要包含一个长度为`0`的"空数字"的情况), 注意上面的递推公式中的`i`是从`1`开始计数的. 而在实现中, 索引实际是从`0`开始计数的. 因此此时递推公式中的 $x_{i-1}x_{i}$ 的实际表示为 `nums[i-2:i] ` (这里将数字保存为了字符串)

```python
class Solution:
    def translateNum(self, num: int) -> int:
        # 为了简化, 我们将数字转为字符串处理
        nums = str(num)
        n = len(nums)
        if n <= 1: # n >=0
            return 1
        # dp[n]表示前n位组成的数字的翻译方案, n从0计数, n取0表示空数字
        dp = [1 for _ in range(n+1)]
        for i in range(2, n+1, 1):
            if "10" <= nums[i-2:i] <= "25": # 10 <= 10*int(nums[i-2])+int(nums[i-1]) <= 25
                dp[i] = dp[i-1] + dp[i-2]
            else:
                dp[i] = dp[i-1]
        return dp[-1]
```

可以发现上述的实现至少有两处可以优化:

+ `dp[i]`只依赖于`dp[i-1]`和`dp[i-2]`, 因此可以使用`滚动数组`优化;
+ 上述实现将数字先转为了字符串, 实际我们不需要转换, 直接基于数字(`int`)也可以完成计算, 减少空间开销;

上述两个方式一起使用, 可以将空间复杂度优化为 $O(1)$.

```python
class Solution:
    def translateNum(self, num: int) -> int:
        a = b = 1
        y = num % 10
        # 可通过`求余`和`求整`运算实现 从右向左 的遍历计算。本题是“对称”的 ，可知从右向左的计算也是正确的
        while num != 0:
            num //= 10
            x = num % 10
            tmp = 10 * x + y
            if 10 <= tmp <= 25:
            	c = a + b  
            else:
                c = a
            a, b = c, a
            y = x
        return a
```



#### [剑指 Offer 48. 最长不含重复字符的子字符串](https://leetcode.cn/problems/zui-chang-bu-han-zhong-fu-zi-fu-de-zi-zi-fu-chuan-lcof/)

> 难度中等580
>
> 请从字符串中找出一个**最长的不包含重复字符的子字符串**，计算该最长子字符串的长度。
>
>  **示例 1:**
>
> ```
> 输入: "abcabcbb"
> 输出: 3 
> 解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
> ```

##### 分析

本题和 [3. 无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/) 一样. 可以用 `滑动窗口` 或者 `动态规划` 求解.

###### 解法一: 滑动窗口

定义一个窗口`[left, right]`, `left`是窗口的左边界, `right`是当前遍历的索引, 是窗口的右边界. 同时将窗口内的元素保存在`哈希表`中. 每当遍历到一个新元素时, 首先判断该元素是否在窗口中出现过(哈希表查询耗时 $O(1)$), 如果出现过, 则说明和当前窗口有重复, 则右移左边界`left`, 同时在窗口`哈希表`移除记录,  直到窗口内没有当前元素为止. 当窗口内没有当前元素时, 将当前元素放入窗口, 同时更新子串长度.

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        left = 0
        cache = set()
        max_len = 0
        for right in range(len(s)):
            while s[right] in cache:
                cache.remove(s[left])
                left += 1
            cache.add(s[right])
            max_len = max(len(cache), max_len)
        return max_len
```

###### 解法二: 动态规划

这是一个单序列最值问题, 可以考虑用动态规划求解. 

令 `dp[n]`表示以字符`s[n]`结尾的 "最长不重复子字符串" 的长度.

<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230424105234.png" alt="image-20230424105227047" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230424105303.png" alt="image-20230424105303131" style="zoom:50%;" />



```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        dic = {}
        res = tmp = 0
        for j in range(len(s)):
            i = dic.get(s[j], -1) # 获取索引 i
            dic[s[j]] = j # 更新哈希表
            tmp = tmp + 1 if tmp < j - i else j - i # dp[j - 1] -> dp[j]
            res = max(res, tmp) # max(dp[j - 1], dp[j])
        return res
```



### D11. 双指针(简单)

#### [剑指 Offer 18. 删除链表的节点](https://leetcode.cn/problems/shan-chu-lian-biao-de-jie-dian-lcof/)

> 难度简单304
>
> 给定单向链表的头指针和一个要删除的节点的值，定义一个函数删除该节点。
>
> 返回删除后的链表的头节点。
>
> **示例 1:**
>
> ```
> 输入: head = [4,5,1,9], val = 5
> 输出: [4,1,9]
> 解释: 给定你链表中值为 5 的第二个节点，那么在调用了你的函数之后，该链表应变为 4 -> 1 -> 9.
> ```

##### 分析

```python
class Solution:
    def deleteNode(self, head: ListNode, val: int) -> ListNode:
        if head.val == val:
            return head.next
        prev, curr = head, head.next
        while curr:
            if curr.val == val:
                prev.next = curr.next
                break
            prev, curr = curr, curr.next
        return head
```

#### [剑指 Offer 22. 链表中倒数第k个节点](https://leetcode.cn/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)

> 难度简单453
>
> 输入一个链表，输出该链表中倒数第k个节点。为了符合大多数人的习惯，本题从1开始计数，即链表的尾节点是倒数第1个节点。
>
> 例如，一个链表有 `6` 个节点，从头节点开始，它们的值依次是 `1、2、3、4、5、6`。这个链表的倒数第 `3` 个节点是值为 `4` 的节点。
>
>  **示例：**
>
> ```
> 给定一个链表: 1->2->3->4->5, 和 k = 2.
> 
> 返回链表 4->5.
> ```

##### 分析

```python
class Solution:
    def getKthFromEnd(self, head: ListNode, k: int) -> ListNode:
        fast = slow = head
        while k > 0 and fast:
            fast = fast.next
            k -= 1
        while fast:
            fast = fast.next
            slow = slow.next
        return slow
```

### D12. 双指针(简单)

#### [剑指 Offer 25. 合并两个排序的链表](https://leetcode.cn/problems/he-bing-liang-ge-pai-xu-de-lian-biao-lcof/)

> 难度简单344
>
> 输入两个递增排序的链表，合并这两个链表并使新链表中的节点仍然是递增排序的。
>
> **示例1：**
>
> ```
> 输入：1->2->4, 1->3->4
> 输出：1->1->2->3->4->4
> ```

##### 分析

```python
class Solution:
    def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
        dummy_head = ListNode()
        p = dummy_head
        while l1 and l2:
            if l1.val <= l2.val:
                p.next = l1
                l1 = l1.next
            else:
                p.next = l2
                l2 = l2.next
            p = p.next
        if l1:
            p.next = l1
        else:
            p.next = l2
        return dummy_head.next
```

#### [剑指 Offer 52. 两个链表的第一个公共节点](https://leetcode.cn/problems/liang-ge-lian-biao-de-di-yi-ge-gong-gong-jie-dian-lcof/)

> 难度简单636
>
> 输入两个链表，找出它们的第一个公共节点。
>
> 如下面的两个链表**：**
>
> [<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230423231742.png" alt="img" style="zoom: 50%;" />](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_statement.png)
>
> 在节点 c1 开始相交。

##### 分析

###### 解法一: 哈希表

先遍历一个链表, 将其路径上的节点保存入哈希表, 再遍历第二个链表, 判断其节点是否在哈希表中, 如果在, 那就是公共节点. 遇到第一个公共节点时返回即可. **此时时间复杂度为 $O(m+n)$, 空间复杂度为 $O(m)$** . 

```python
class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:
        if not headA or not headB:
            return None
        nodes = set()
        while headA:
            nodes.add(headA)
            headA = headA.next
        while headB:
            if headB in nodes:
                return headB
            headB = headB.next
        return None
```

###### 解法二: 双指针

利用双指针, 假设目标节点`a`的反向路径长度为 `A+C`, 目标节点`b`的反向路径长度为`B+C`, 其中`C`为两个反向路径的公共部分, `A`,`B`为两个反向路径的不同部分. 则令指针`p1`沿着 `A+C+B+C` 遍历, 令指针`p2`沿着 `B+C+A+C` 遍历, 这两个遍历长度相同, 因此可以同时在一个循环中完成. 如果两个路径有相交部分, 则一定会在遍历时同时遇到(此时`p1==p2`); 否则, 则两个指针一定会同时遇到`null`, 此时还是`p1==p2`, 因此`p1==p2`就是循环的退出条件. 退出循环后直接返回`p1`即可; **此时时间复杂度为 $O(m+n)$, 空间复杂度为 $O(1)$**. 

```python
class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:
        if not headA or not headB:
            return None
        p1 = headA
        p2 = headB
        while p1 != p2:
            p1 = p1.next if p1 else headB
            p2 = p2.next if p2 else headA
        return p1
```

### D13. 双指针(简单)

#### [剑指 Offer 21. 调整数组顺序使奇数位于偶数前面](https://leetcode.cn/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof/)

> 难度简单296
>
> 输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有奇数在数组的前半部分，所有偶数在数组的后半部分。
>
>  **示例：**
>
> ```
> 输入：nums = [1,2,3,4]
> 输出：[1,3,2,4] 
> 注：[3,1,2,4] 也是正确的答案之一。
> ```

##### 分析

类似快速排序的`paritition`方法中的划分方法, 容易实现如下:

```python
class Solution:
    def exchange(self, nums: List[int]) -> List[int]:
        left = 0
        for i in range(len(nums)):
            if nums[i] % 2 == 1:
                if i != left:
                	nums[left], nums[i] = nums[i], nums[left]
                left += 1
        return nums
```

#### [剑指 Offer 57. 和为s的两个数字](https://leetcode.cn/problems/he-wei-sde-liang-ge-shu-zi-lcof/)

> 难度简单254
>
> 输入一个**递增排序**的数组和一个数字s，在数组中查找两个数，使得它们的和正好是s。如果有多对数字的和等于s，则输出任意一对即可。
>
>  **示例 1：**
>
> ```
> 输入：nums = [2,7,11,15], target = 9
> 输出：[2,7] 或者 [7,2]
> ```

##### 分析

###### 解法一: 利用哈希表

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        cache = set()
        for a in nums:
            b = target - a
            if b in cache:
                return [a, b]
            cache.add(a)
        return []
```

这个方式的时间复杂度为 $O(n)$, 空间复杂度为 $O(n)$. 注意**这里没有利用数组是有序的信息**.

###### 解法二: 双指针

本题中的数组是**升序数组**, 可以利用双指针将空间复杂度降低为 $O(1)$.

<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230424113734.png" alt="image-20230424113734206" style="zoom:60%;" />



```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        # 对撞双指针
        i = 0
        j = len(nums) - 1
        while i != j:
            curr_sum = nums[i] + nums[j]
            if curr_sum == target:
                return [nums[i], nums[j]]
            elif curr_sum < target:
                i += 1
            else:
                j -= 1
        return []
```



#### [剑指 Offer 58 - I. 翻转单词顺序](https://leetcode.cn/problems/fan-zhuan-dan-ci-shun-xu-lcof/)

> 难度简单286
>
> 输入一个英文句子，翻转句子中单词的顺序，但单词内字符的顺序不变。为简单起见，标点符号和普通字母一样处理。例如输入字符串"I am a student. "，则输出"student. a am I"。
>
>  **示例 1：**
>
> ```
> 输入: "the sky is blue"
> 输出: "blue is sky the"
> ```
>
> - 无空格字符构成一个单词。
>
> - 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。
> - 如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。

##### 分析

可以借助Python内置方法实现:

```python
class Solution:
    def reverseWords(self, s: str) -> str:
        return " ".join(s.split()[::-1])
```

<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230424114532.png" alt="image-20230424114532148" style="zoom:50%;" />

```python
class Solution:
    def reverseWords(self, s: str) -> str:
        s = s.strip() # 删除首尾空格
        i = j = len(s) - 1
        res = []
        while i >= 0:
            while i >= 0 and s[i] != ' ': 
                i -= 1 # 搜索首个空格
            res.append(s[i + 1: j + 1]) # 添加单词
            while s[i] == ' ': 
                i -= 1 # 跳过单词间空格
            j = i # j 指向下个单词的尾字符
        return ' '.join(res) # 拼接并返回
```



### D14. 搜索与回溯算法(中等)

#### [剑指 Offer 12. 矩阵中的路径](https://leetcode.cn/problems/ju-zhen-zhong-de-lu-jing-lcof/)

> 难度中等787
>
> 给定一个 `m x n` 二维字符网格 `board` 和一个字符串单词 `word` 。如果 `word` 存在于网格中，返回 `true` ；否则，返回 `false` 。
>
> 单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。
>
> 例如，在下面的 3×4 的矩阵中包含单词 "ABCCED"（单词中的字母已标出）。
>
> <img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230424151453.jpeg" alt="img" style="zoom:50%;" />
>
> ```python
> 输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
> 输出：true
> ```

##### 分析

本问题是典型的**矩阵搜索&回溯问题**，可使用 `深度优先搜索（DFS）+ 剪枝` 解决。

+ **深度优先搜索**： 可以理解为**暴力法遍历矩阵中所有字符串可能性**。DFS 通过递归，先朝一个方向搜到底，再回溯至上个节点，沿另一个方向搜索，以此类推。
+ **剪枝**： 在搜索中，遇到 这条路不可能和目标字符串匹配成功 的情况（例如：此矩阵元素和目标字符不同、此元素已被访问），则应立即返回，称之为 `可行性剪枝 `

这里判断的是能不能找到, 因此只需要返回`True`或者`False`即可

```python
class Solution:
    def exist(self, board: List[List[str]], word: str) -> bool:

        def dfs(i, j, k):
            if not 0 <= i < len(board) or not 0 <= j < len(board[0]) or board[i][j] != word[k]: 
                # 索引越界 或者 当前字符与目标字符不一致, 则表示当前的遍历没有找到.
                return False
            if k == len(word) - 1: 
                # 当前索引字符和目标字符一致, 且这是最后一个字符,
                # 表示整个单词都已经被找到, 立刻返回
                return True
            # 将当前位置字符改为一个特殊字符, 表示已经访问过
            board[i][j] = ''
            # 继续搜索 下(i+1), 右(上-1), 右(j+1), 左(j-1), 此时目标字符为word[k+1] 
            res = dfs(i+1, j, k+1) or dfs(i-1, j, k+1) or dfs(i, j+1, k+1) or dfs(i, j-1, k+1)
            # 还原当前字符
            board[i][j] = word[k]
            return res

        for i in range(len(board)):
            for j in range(len(board[0])):
                # 遍历整个图, 尝试从任意一点开始搜索
                if dfs(i, j, 0): 
                    return True
        return False
```

若矩阵的长宽分别为 $m$ , $n$ , 目标字符串的长度为 $k$, 则算法的时间复杂度为 $O(3^kmn)$. 这是因为要遍历矩阵中的所有点作为起点, 遍历的时间复杂度为 $O(mn)$, 每个起点处都需要遍历长度为 $k$ 的所有组合方案, 时间复杂度为 $O(3^k)$.  空间复杂度为 $O(k)$

#### [剑指 Offer 13. 机器人的运动范围](https://leetcode.cn/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/)

> 难度中等626
>
> 地上有一个`m`行`n`列的方格，从坐标 `[0,0]` 到坐标 `[m-1,n-1]` 。一个机器人从坐标 `[0, 0] `的格子开始移动，它每次可以**向左、右、上、下**移动一格（不能移动到方格外），也**不能进入行坐标和列坐标的数位之和大于k的格子**。例如，当k为18时，机器人能够进入方格 [35, 37] ，因为3+5+3+7=18。但它不能进入方格 [35, 38]，因为3+5+3+8=19。请问该机器人能够到达多少个格子？
>
> ```
> 输入：m = 2, n = 3, k = 1
> 输出：3
> ```

##### 分析

本题也是典型的**矩阵搜索&回溯问题**. 可使用 `深度优先搜索（DFS）+ 剪枝` 解决:

+ **深度优先搜索**： 可以理解为**暴力法模拟机器人在矩阵中的所有路径**。DFS 通过递归，先朝一个方向搜到底，再回溯至上个节点，沿另一个方向搜索，以此类推。
+ **剪枝**： 在搜索中，**遇到数位和超出目标值、此元素已访问，则应立即返回**，称之为 `可行性剪枝` 

首先根据题意, 我们需要实现索引数位和算法, 然后使用`dfs`从`(0,0)`开始遍历. 这里只让机器人`向右`或者`向下`移动, 可以证明, 只做这两种移动也可以访问所有的可达解.

> [剑指 Offer 13. 机器人的运动范围（ 回溯算法，DFS / BFS ，清晰图解） - 机器人的运动范围 - 力扣（LeetCode）](https://leetcode.cn/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/solution/mian-shi-ti-13-ji-qi-ren-de-yun-dong-fan-wei-dfs-b/)

```python
def digitsum(n):
    """计算数位和, 例如数字35, 3+5=8"""
    ans = 0
    while n:
        ans += n % 10
        n //= 10
    return ans

class Solution:
    def movingCount(self, m: int, n: int, k: int) -> int:
        # 用一个m*n的布尔数组记录格子是够已经访问过
        # 因此本题统计的是一共能移动到多少个不同的格子,
        # 因此移动到一个已经访问过的格子其实没有意义,
        # 因此这里设置一个是否访问过的布尔数组
        visited = [
            [False for _ in range(n)]
            for _ in range(m)
        ]

        def dfs(i,j) -> int:
            if not 0<=i<m or not 0<=j<n or visited[i][j] or digitsum(i)+digitsum(j) > k:
                # 遇到索引越界/数位和超出目标值/此元素已访问，则应立即返回
                return 0
            visited[i][j] = True
            # 当前格子已经访问过了, 所以要+1, 此外还要看从这个格子出发, 还能继续访问多少个格子
            # 这里只让机器人向右或者向下移动, 可以证明, 只做这两种移动也可以访问所有的可达解
            return 1 + dfs(i+1, j) + dfs(i, j+1)

        return dfs(0,0)
```

时间复杂度为 $O(mn)$, 空间复杂度为 $O(m,n)$



### D15. 搜索与回溯算法(中等)

#### [剑指 Offer 34. 二叉树中和为某一值的路径](https://leetcode.cn/problems/er-cha-shu-zhong-he-wei-mou-yi-zhi-de-lu-jing-lcof/)

> 难度中等420
>
> 给你二叉树的根节点 `root` 和一个整数目标和 `targetSum` ，找出所有 **从根节点到叶子节点** 路径总和等于给定目标和的路径。
>
> **叶子节点** 是指没有子节点的节点。
>
> ```
> 输入：root = [5,4,8,11,null,13,4,7,2,null,null,5,1], targetSum = 22
> 输出：[[5,4,11,2],[5,8,4,5]]
> ```

##### 分析

本题和 [112. 路径总和](https://leetcode.cn/problems/path-sum/) 一样, 只不过这里需要记录路径.

```python
import copy
class Solution:
    def pathSum(self, root: TreeNode, target: int) -> List[List[int]]:
        res = []
        path = [] # 记录一次遍历的路径
        def dfs(root: TreeNode, target: int):
            """深度搜索路径, 
            如果节点值等于target, 且是叶子节点, 则说明找到了一条路径
            """
            if not root:
                return
            path.append(root.val)
            if root.val == target:
                if not root.left and not root.right:
                    res.append(copy.copy(path))
                    path.pop() # 恢复递归调用前的状态
                    return
            target = target - root.val
            dfs(root.left, target)
            dfs(root.right, target)
            path.pop() # 恢复递归调用前的状态
        
        dfs(root, target)
        return res
```





### D16. 排序(简单)



### D17. 排序(中等)



### D18. 搜索与回溯算法(中等)

#### [剑指 Offer 55 - I. 二叉树的深度](https://leetcode.cn/problems/er-cha-shu-de-shen-du-lcof/)

> 难度简单241
>
> 输入一棵二叉树的根节点，求该树的深度。从根节点到叶节点依次经过的节点（含根、叶节点）形成树的一条路径，最长路径的长度为树的深度。



#### [剑指 Offer 55 - II. 平衡二叉树](https://leetcode.cn/problems/ping-heng-er-cha-shu-lcof/)

> 难度简单354
>
> 输入一棵二叉树的根节点，判断该树是不是平衡二叉树。如果某二叉树中任意节点的左右子树的深度相差不超过1，那么它就是一棵平衡二叉树。



### D19. 搜索与回溯算法(中等)



### D20. 分治算法(中等)





### D26. 字符串(中等)



### D27. 栈与队列(困难)

#### [剑指 Offer 59 - I. 滑动窗口的最大值](https://leetcode.cn/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof/)

> 难度困难574
>
> 给定一个数组 `nums` 和滑动窗口的大小 `k`，请找出所有滑动窗口里的最大值。
>
> **示例:**
>
> ```
> 输入: nums = [1,3,-1,-3,5,3,6,7], 和 k = 3
> 输出: [3,3,5,5,6,7] 
> 解释: 
> 
>   滑动窗口的位置                最大值
> ---------------               -----
> [1  3  -1] -3  5  3  6  7       3
>  1 [3  -1  -3] 5  3  6  7       3
>  1  3 [-1  -3  5] 3  6  7       5
>  1  3  -1 [-3  5  3] 6  7       5
>  1  3  -1  -3 [5  3  6] 7       6
>  1  3  -1  -3  5 [3  6  7]      7
> ```

##### 分析

###### 解法一: 借助堆

容易想到一种暴力解法: 用两个指针保存每个窗口内的$k$个元素, 每次都遍历窗口, 获取窗口内最大值. 如果数组长度为$n$, 则窗口的数量为`n-k+1`, 因此此种算法的复杂度为 $O((n-k+1)k)=O(nk)$. 显然这种方式不是最好的解法, 下面考虑优化: 

由于需要实时获取滑动窗口内的最大值, 容易想到使用`最大堆`来存储窗口中的元素. 初始时, 将数组的前$k$个元素保存入`最大堆`, 每当窗口右移时, 我们将新的元素放入`最大堆`, 同时需要从堆中移除当前已不在窗口中的元素. 由于从堆中移除一个任意位置的元素复杂度较高,  最好的方式是移除堆顶元素. 因此我们将窗口内元素保存为一个元组, 即元素值和对应的索引. 当每次从堆中取出最大值时, 首先判断堆顶元素的索引是否在窗口内, 如否, 则弹出堆顶, 直到堆顶元素在窗口内为止.

根据上述的分析, 当数组本身是一个单调升序数组时, 堆内需要保存数组全部的元素, 此时为最差时间复杂度. 此时在堆内插入元素的复杂度为$O(\log{n})$, 因此总的时间复杂度为 $O(n\log{n})$. 

```python
import heapq
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        max_heap = []
        left = 1 - k
        right = 0
        res = []
        while right < len(nums):
            heapq.heappush(max_heap, (-1 * nums[right], right))
            if left >= 0:
                heap_top = max_heap[0]
                while not left <= heap_top[1] <= right:
                    heapq.heappop(max_heap)
                    heap_top = max_heap[0]
                res.append(-1 * heap_top[0])
            right += 1
            left += 1
        return res
```

###### 解法二: 单调队列

> `单调队列`是一种主要用于解决`滑动窗口`类问题的数据结构, 即在长度为`n`的序列中, 求每个长度为`m`的窗口区间的最值. 它的时间复杂度为 $O(n)$, 在此类问题中要优于`ST表`和`线段树`.
>
> [算法学习笔记(66): 单调队列 - 知乎](https://zhuanlan.zhihu.com/p/346354943)
>
> 单调队列使用`双端队列(deque)`实现, Python中的双端队列为`collections.deque`, 其主要操作包括:
>
> + `append(x)`: 添加元素`x`到队列右端;
> + `appendleft(x)`: 添加元素`x`到队列左端;
> + `pop()`: 弹出并返回队列右端元素;
> + `popleft()`: 弹出并返回队列左端元素;

单调队列的基本思想是维护一个`双端队列deque`, 遍历序列, 仅当一个元素有可能称为某个区间的最值时才保存它.

例如, 有序列`[1,3,6,2,5,1,7]`, 现在窗口大小为`4`, 求每个窗口的最大值. 

1. 一开始窗口内有四个元素, 注意此时前两个元素`1,3`小于第三个元素`6`, 因此它们两个不可能称为任意一个窗口的最值, 因此队列中仅保存`[6,2]`;

<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230427101321.png" alt="image-20230427101313802" style="zoom:33%;" />

2. 窗口右移, 此时新的元素`5`大于`2`, 且排在`2`的后面, 因此`2`也不可能成为任意一个窗口的最值, 因此将`2`弹出队列(`pop_back`), `5`压入队列, 此时队列中数据为`[6,5]`

<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230427101750.png" alt="image-20230427101749849" style="zoom:33%;" />

2. 窗口继续右移, 此时新的元素`1`虽然小于窗口内的其他元素, 但是其排在最后, 因此有可能称为后面窗口区间的最大值, 因此压入队列, 此时队列中的数据为`[6,5,1]`

<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230427101822.png" alt="image-20230427101822241" style="zoom:33%;" />

2. 窗口移动到尽头, 此时`6`不在窗口内, 弹出队列(`pop_front`). 且新的元素`7`比前面的窗口内元素都大, 故将队列内其他的元素都弹出(`pop_front`), 然后将`7`压入队列. 此时队列内数据为`[7]`

<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230427101839.png" alt="image-20230427101839677" style="zoom:33%;" />

观察就会发现，我们维护的这个队列总是`单调递减`的。如果维护区间最小值，那么维护的队列就是`单调递增`的。这就是为什么叫`单调队列`.

总结来说, 我们使用单调队列解决这类问题, 遍历数组时, 保证单调队列`deque`满足:

1. `deque`内仅保存当前窗口区间内的元素, 当窗口右移, 之前左边界的元素需要从`deque`中删除(此时是从队列**左侧弹出**);
2. `deque`内的元素**非严格递减**, 每次保存新元素`x`(窗口右边界)之前, 需要将`deque`内小于`x`的元素全部弹出(此时是从队列**右侧弹出**);

```python
import collections
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        deque = collections.deque()
        left = 1 - k
        right = 0
        res = []
        while right < len(nums):
            # 当窗口右移, 之前左边界的元素需要从`deque`中删除(此时是从队列**左侧弹出**)
            if left > 0 and deque[0] == nums[left-1]:
                deque.popleft()
            # 每次保存新元素`x`(窗口右边界)之前, 
            # 需要将`deque`内小于`x`的元素全部弹出(此时是从队列**右侧弹出**)
            while deque and deque[-1] < nums[right]:
                deque.pop()
            deque.append(nums[right])
            if left >= 0:
                res.append(deque[0])
            right += 1
            left += 1
        return res
```

时间复杂度: 双端队列`deque`两端压入和弹出操作的时间复杂度都为 $O(1)$, 因此整个时间复杂度为 $O(n)$;

空间复杂度: `deque`最多有`k`个元素, 因此空间复杂度为 $O(k)$



#### [剑指 Offer 59 - II. 队列的最大值](https://leetcode.cn/problems/dui-lie-de-zui-da-zhi-lcof/)

> 难度中等464
>
> 请定义一个队列并实现函数 `max_value` 得到队列里的最大值，要求函数`max_value`、`push_back` 和 `pop_front` 的**均摊**时间复杂度都是O(1)。
>
> 若队列为空，`pop_front` 和 `max_value` 需要返回 -1
>
> **示例 1：**
>
> ```
> 输入: 
> ["MaxQueue","push_back","push_back","max_value","pop_front","max_value"]
> [[],[1],[2],[],[],[]]
> 输出: [null,null,null,2,1,2]
> ```

##### 分析(借助双端队列)

如下面的动图所示, 我们利用一个辅助双端队列`deque`来保存主队列`queue`中每一时刻的最大值.

+ `push_back`: 当新元素`x`压入主队列`queue`时, 将辅助队列`deque`右侧所有小于`x`的值全部弹出(从右侧弹出), 然后再将`x`也压入辅助队列`deque`;
+ `max_value`: 当获取当前最大值时, 直接返回辅助队列的最左侧元素即可;
+  `pop_front`: 当弹出主队列最左侧元素`y`时, 判断辅助队列最左侧元素是否等于`y`, 如果等于, 则同样弹出队列;

<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230427131716.gif" alt="fig3.gif" style="zoom:50%;" />

```python
import collections
class MaxQueue:
    def __init__(self):
        # 主队列
        self.queue = collections.deque()
        # 辅助队列(必须是一个双端队列)
        self.deque = collections.deque()

    def max_value(self) -> int:
        # 当获取当前最大值时, 直接返回辅助队列的最左侧元素即可;
        return self.deque[0] if self.deque else -1

    def push_back(self, value: int) -> None:
        # 当新元素`x`压入主队列`queue`时, 
        # 将辅助队列`deque`右侧所有小于`x`的值全部弹出(从右侧弹出), 
        # 然后再将`x`也压入辅助队列`deque`;
        self.queue.append(value)
        while self.deque and self.deque[-1] < value:
            self.deque.pop()
        self.deque.append(value)

    def pop_front(self) -> int:
        # 当弹出主队列最左侧元素`y`时, 
        # 判断辅助队列最左侧元素是否等于`y`, 
        # 如果等于, 则同样弹出队列;
        if not self.queue: 
            return -1
        val = self.queue.popleft()
        if val == self.deque[0]:
            self.deque.popleft()
        return val
```





### D28. 搜索与回溯算法(困难)



### D29. 动态规划(困难)

#### [剑指 Offer 49. 丑数](https://leetcode.cn/problems/chou-shu-lcof/)

> 难度中等455
>
> 我们把只包含质因子 2、3 和 5 的数称作丑数（Ugly Number）。求按从小到大的顺序的第 n 个丑数。
>
>  **示例:**
>
> ```
> 输入: n = 10
> 输出: 12
> 解释: 1, 2, 3, 4, 5, 6, 8, 9, 10, 12 是前 10 个丑数。
> ```
>
> **说明:** 
>
> 1. `1` 是丑数。
> 2. `n` **不超过**1690。





#### [剑指 Offer 19. 正则表达式匹配](https://leetcode.cn/problems/zheng-ze-biao-da-shi-pi-pei-lcof/)

> 难度困难535
>
> 请实现一个函数用来匹配包含`'. '`和`'*'`的正则表达式。模式中的字符`'.'`表示任意一个字符，而`'*'`表示它前面的字符可以出现任意次（含0次）。在本题中，匹配是指字符串的所有字符匹配整个模式。例如，字符串`"aaa"`与模式`"a.a"`和`"ab*ac*a"`匹配，但与`"aa.a"`和`"ab*a"`均不匹配。
>
> **示例 1:**
>
> ```
> 输入:
> s = "aa"
> p = "a"
> 输出: false
> 解释: "a" 无法匹配 "aa" 整个字符串。
> ```

##### 分析

> 本题和 [10. 正则表达式匹配](https://leetcode.cn/problems/regular-expression-matching/) 相同



#### [剑指 Offer 60. n个骰子的点数](https://leetcode.cn/problems/nge-tou-zi-de-dian-shu-lcof/)

> 难度中等548
>
> 把n个骰子扔在地上，**所有骰子朝上一面的点数之和为`s`**。输入n，**打印出`s`的所有可能的值出现的概率**。
>
>  你需要用一个浮点数数组返回答案，其中第 `i` 个元素代表这 `n` 个骰子所能掷出的点数集合中第` i `小的那个的概率。
>
>  **示例 1:**
>
> ```
> 输入: 1
> 输出: [0.16667,0.16667,0.16667,0.16667,0.16667,0.16667]
> ```





### D30. 分治算法(困难)

#### [剑指 Offer 51. 数组中的逆序对](https://leetcode.cn/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

> 难度困难997
>
> 在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个`逆序对`。输入一个数组，求出这个数组中的逆序对的总数。
>
>  **示例 1:**
>
> ```
> 输入: [7,5,6,4]
> 输出: 5
> ```

##### 分析

`逆序数`就是冒泡排序时交换的次数. 暴力的解法就是遍历数组所有可能的数字对, 判断是够逆序并统计. 此方法的时间复杂度为 $O(n^2)$ ,  会超时, 因此我们必须寻找更高效的算法.

`归并排序`与`逆序对`是息息相关的。归并排序体现了 “分而治之” 的算法思想，具体为：

+ **分**： 不断将数组从中点位置划分开（即二分法），将整个数组的排序问题转化为子数组的排序问题；
+ **治**： 划分到子数组长度为 1 时，开始向上合并，不断将 较短排序数组 合并为 较长排序数组，直至合并至原数组时完成排序；

<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230424173806.png" alt="Picture1.png" style="zoom:40%;" />

**合并阶段** 本质上是 **合并两个排序数组** 的过程，而每当遇到 `左子数组当前元素 > 右子数组当前元素` 时，意味着`左子数组当前元素至末尾元素` 与 `右子数组当前元素` 构成了若干 `逆序对` 。如下图所示:

<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230424174122.png" alt="image-20230424174122545" style="zoom:60%;" />

因此，考虑在归并排序的`合并`阶段统计`逆序对`数量，完成归并排序时，也随之完成所有逆序对的统计。

```python
class Solution:
    def merge_sort(self, nums, left, right) -> int:
        if left >= right:
            return 0
        mid = (right+left) // 2
        left_count = self.merge_sort(nums, left, mid)
        right_count = self.merge_sort(nums, mid+1, right)
        return self.merge(nums, left, right, mid) + left_count + right_count
    
    def merge(self, nums, left, right, mid) -> int:
        # 创建一个临时数组, 保存排序后的数组分区
        temp = [0 for _ in range(right - left + 1)]
        i = left
        j = mid + 1
        k = 0
        count = 0
        while i <= mid and j <= right:
            if nums[i] > nums[j]:
                # 统计逆序数!!
                count += mid - i + 1
                temp[k] = nums[j]
                j += 1
            else:
                temp[k] = nums[i]
                i += 1
            k += 1
        while i <= mid:
            temp[k] = nums[i]
            i += 1
            k += 1
        while j <= right:
            temp[k] = nums[j]
            j += 1
            k += 1
        # 把新数组中的数覆盖nums数组
        nums[left : right+1] = temp
        return count

    def reversePairs(self, nums: List[int]) -> int:
        return self.merge_sort(nums, 0, len(nums)-1)
```





## 第二版(进阶)





