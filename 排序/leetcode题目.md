# leetcode排序题目汇总

[toc]

#### [912. 排序数组](https://leetcode.cn/problems/sort-an-array/)

> 难度中等829
>
> 给你一个整数数组 `nums`，请你将该数组升序排列。
>
> **示例 1：**
>
> ```
> 输入：nums = [5,2,3,1]
> 输出：[1,2,3,5]
> ```
>
> **提示：**
>
> - `1 <= nums.length <= 5 * 104`
> - `-5 * 104 <= nums[i] <= 5 * 104`

##### 分析

可以实现 $O(n\log{n})$ 排序算法中的任意一种: `快速排序`, `归并排序`, `堆排序`

由于本题中, 数组数值范围很大, 因此不适合使用 `计数排序`

#### [347. 前 K 个高频元素](https://leetcode.cn/problems/top-k-frequent-elements/)

> 难度中等1536
>
> 给你一个整数数组 `nums` 和一个整数 `k` ，请你返回其中出现频率前 `k` 高的元素。你可以按 **任意顺序** 返回答案。
>
> **进阶：**你所设计算法的时间复杂度 **必须** 优于 `O(nlogn)` ，其中 `n` 是数组大小。
>
> **示例 1:**
>
> ```
> 输入: nums = [1,1,1,2,2,3], k = 2
> 输出: [1,2]
> ```
>
> **示例 2:**
>
> ```
> 输入: nums = [1], k = 1
> 输出: [1]
> ```

##### 分析(借助**堆**)

首先遍历整个数组，并使用哈希表记录每个数字出现的次数，并形成一个「`频率Map<num, freq>`」. 容易想到对`频率Map`按照`freq(频率)`排序, 但是排序的平均时间复杂度为 $O(n\log{n})$, 已经超过了题目要求.

由于我们只需要前`k`高频的元素, 容易想到通过`最小堆`来实现.

首先定义一个最小堆, 然后遍历 `频率Map` 的`(num, freq)`:

- 首先将元组插入堆, 按照 频率 排序;
- 然后判断堆的大小, 如果大于`k`, 则弹出堆顶元素(频率最小的元组);

遍历完成后, 堆内就是频率前`k`高的元素.

借助`heapq`, 实现如下:

```python
import heapq
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        # 出现频率hashmap
        freq_map = {}
        # 首先遍历整个数组，并使用哈希表记录每个数字出现的次数 $O(n)$
        for i in nums:
            freq_map[i] = freq_map.get(i, 0) + 1
        # 定义一个最小堆, 大小为 k
        min_heap = []
        # heapq 不支持通过 key= 来自定义排序比较器
        # 如果 heapq 中放入的是元组，那么元组的第一个元素会用于大小比较
        # 如果 heapq 中放入的是 自定义类 时，可以通过实现 __lt__ 方法来比较元素大小
        for num, freq in freq_map.items():
            # 首先压入最小堆, 通过 heapq.heappush() 方法
            heapq.heappush(min_heap, (freq, num))
            # 然后检查堆的大小是否超过k, 如果超过则弹出堆顶(最小值)
            if len(min_heap) > k:
                heapq.heappop(min_heap)
        # 由于本题允许以任意顺序返回答案, 因此可以直接返回堆的列表
        # 如果希望按照大小顺序返回, 则可通过不断调用 heappop 的方式实现
        return [x[1] for x in min_heap]
```

时间复杂度为 $O(n\log{k})$

> `heapq` 不支持通过 `key=` 来自定义排序比较器, 如果 `heapq` 中放入的是`元组`，那么`元组`的第一个元素会用于大小比较; 如果 `heapq` 中放入的是 `自定义类` 时，可以通过实现 `__lt__`方法来比较元素大小.
>
> 详见 [692. 前K个高频单词](https://leetcode.cn/problems/top-k-frequent-words/)

#### [692. 前K个高频单词](https://leetcode.cn/problems/top-k-frequent-words/)

> 难度中等539
>
> 给定一个单词列表 `words` 和一个整数 `k` ，返回前 `k` 个出现次数最多的单词。
>
> 返回的答案应该按单词出现频率由高到低排序。如果不同的单词有相同出现频率， **按字典顺序** 排序。
>
>  
>
> **示例 1：**
>
> ```
> 输入: words = ["i", "love", "leetcode", "i", "love", "coding"], k = 2
> 输出: ["i", "love"]
> 解析: "i" 和 "love" 为出现次数最多的两个单词，均为2次。
>     注意，按字母顺序 "i" 在 "love" 之前。
> ```

##### 分析(基于堆排序)

本题和上一题 [347. 前 K 个高频元素](https://leetcode.cn/problems/top-k-frequent-elements/) 几乎一模一样, 因此这里可以通过维持一个最小堆来解决. 但是这里有一个不同之处, **本题要求按照两种顺序来对元素排序**: 先按照单词的频率高低排序(高的排在前面), 如果频率一样, 则进一步按照单词的字典序排序(小的排在前面).

由于`heapq`不支持`key`关键字来指定比较器, 如果只需要按照一种顺序可以使用`元组`(类似[347. 前 K 个高频元素](https://leetcode.cn/problems/top-k-frequent-elements/)的处理), 如果需要按照两种顺序则必须将元素用一个自定义类封装起来, 并实现`__it__()`方法.

如下的实现展示了自定义类和`__it__`方法的实现细节.

```python
import heapq
class Node:
    """
    由于heapq不支持key关键字, 而本题要求按照两种顺序排序
    为此我们需要将堆中元素封装在Node类中, 并实现 __it__ 方法
    """
    def __init__(self, freq, word):
        self.word = word
        self.freq = freq

    def __lt__(self, other):
        """
        先比较频率大小, 频率小的排在前面, 
        这样在倒序弹出堆后就变为了题目要求的"按单词出现频率由高到低排序";
        如果两个元素的频率相同, 则进一步比较单词的字典序, 字典序大的排在前面,
        这样在倒序弹出堆后就变为了题目要求的"按字典顺序排序(从小到大)";
        """
        if self.freq < other.freq:
            return True
        elif self.freq == other.freq:
            return self.word > other.word
        else:
            return False
    
    def __str__(self):
        return f"{self.word}-{self.freq}"

class Solution:
    def topKFrequent(self, words: List[str], k: int) -> List[str]:
        # 遍历, 统计词语频率
        freq_map = {}
        for w in words:
            freq_map[w] = freq_map.get(w, 0) + 1
        # 构建一个大小为k的最小堆
        min_heap = []
        for w, f in freq_map.items():
            heapq.heappush(min_heap, Node(f, w))
            if len(min_heap) > k:
                heapq.heappop(min_heap)
        # 倒序弹出堆顶
        res = [None for _ in range(k)]
        for i in range(k-1, -1, -1):
            res[i] = heapq.heappop(min_heap).word
        return res
```

本题也可以通过排序解决, 时间复杂度和上述实现基本一致.

#### [215. 数组中的第K个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/)

> 难度中等2141
>
> 给定整数数组 `nums` 和整数 `k`，请返回数组中第 `k` 个最大的元素。
>
> 请注意，你需要找的是数组排序后的第 `k` 个最大的元素，而不是第 `k` 个不同的元素。
>
> **重要**: 你必须设计并实现时间复杂度为 `O(n)` 的算法解决此问题。
>
> **示例 1:**
>
> ```
> 输入: [3,2,1,5,6,4], k = 2
> 输出: 5
> ```

##### 分析(基于堆)

类似 [347. 前 K 个高频元素](https://leetcode.cn/problems/top-k-frequent-elements/), 我们可以构造一个大小为`k`的最小堆, 然后遍历数组, 依次入堆, 同时判断堆的大小是否超过了`k`, 如果超过则弹出堆顶. 遍历结束后, 堆顶元素就是第`k`大的值.

但是由于堆操作的时间复杂度为 $O(n\log{k})$, 因此实际上超过了本题的时间限制.

具体实现类似于 [347. 前 K 个高频元素](https://leetcode.cn/problems/top-k-frequent-elements/)

##### 分析(基于**快速排序**)

要找数组中的第`k`个最大元素, 就是**找排序(升序)后的数组中索引为 `n-k` 的元素**.

我们可以先对数组做一次 快速排序, 然后返回对应索引处的元素. 但是快排的时间复杂度为 $O(n\log{n})$, 显然超过了题目的时间要求. 虽然快排不能满足本题的时间限制, 但是我们可以根据快排的思想, 设计一个更快的**快速选择算法**.

首先回顾一下快速排序的算法思路, 快速排序是典型的 分治法. 我们对数组 `arr[l...r]` 做快速排序的过程是(Lomuto分割方案):

- **分割**: 将数组`arr[l...r]`分割为两个子数组, 并返回分割点的索引`p`, 使得 `arr[l...p-1]`中的每个元素都小于`arr[p]`, `arr[p+1...r]`中的每个元素都大于等于`arr[p]` .
- **解决**: 递归调用快速排序, 对两个子数组`arr[l...p-1]`和`arr[p+1...r]`排序
- **合并**: 由于子数组都是原地排序的, 因此不需要合并操作

其中最关键的就是`分割算法`, 在分割后, 我们实际上确定了分割点元素`arr[p]`在排序数组中的正确索引`p` (其左右子数组依然是无序的, 需要进一步递归分割). 因此, 我们可以在快速排序时不断判断分割点的索引`p`和我们的目标索引`n-k`的关系:

- 如果`p == n-k`, 则直接返回分割点取值即可.
- 如果`p < n-k`, 则只需要进一步递归 右侧的子数组;
- 如果`p > n-k`, 则只需要进一步递归 左侧的子数组;

如果我们在分割算法中, 每次**随机**选择`基准值pivot`(也就是分割点), 那么可以证明, 上述算法的时间代价期望为 $O(n)$. 这种算法又称为 **线性快速选择算法**. 实现如下:

```python
class Solution:
    def partition(self, arr, lo, hi):
        """
         用pivot分割序列, 使得pivot之前的元素都<pivot, 而pivot之后的元素都>=pivot, 
         并返回分割点pivot的索引
        """
        pivot = arr[hi] # 选择序列的最后一个元素作为基准值
        i = lo
        for j in range(lo, hi, 1): # lo to hi - 1
            if arr[j] < pivot: # 这里也可以是 <=
                # 交换arr[j]和arr[i], 然后i前移
                arr[i], arr[j] = arr[j], arr[i]
                i += 1
        arr[i], arr[hi] = arr[hi], arr[i] # pivot的索引是hi
        # 分割完成, 此时i就是pivot的索引
        return i
    
    def random_partition(self, arr:List[int], lo: int, hi: int) -> int:
        # 随机选择基准值, 可以证明此时期望时间复杂度为 $O(n)$
        r = random.randint(lo, hi)
        arr[hi], arr[r] = arr[r], arr[hi]
        return self.partition(arr, lo, hi)
    
    def quick_select(self, arr, lo, hi, target):
        p = self.random_partition(arr, lo, hi)
        if p == target:
            return arr[p]
        elif p < target:
            return self.quick_select(arr, p+1, hi, target)
        else:
            return self.quick_select(arr, lo, p-1, target)

    def findKthLargest(self, nums: List[int], k: int) -> int:
        return self.quick_select(nums, 0, len(nums) - 1, len(nums) - k)
```

#### [414. 第三大的数](https://leetcode.cn/problems/third-maximum-number/)

>难度简单411
>
>给你一个非空数组，返回此数组中 **第三大的数** 。如果不存在，则返回数组中最大的数。
>
>**进阶：**你能设计一个时间复杂度 `O(n)` 的解决方案吗？
>
>**示例 1：**
>
>```
>输入：[3, 2, 1]
>输出：1
>解释：第三大的数是 1 。
>```
>
>**示例 2：**
>
>```
>输入：[1, 2]
>输出：2
>解释：第三大的数不存在, 所以返回最大的数 2 。
>```
>
>**示例 3：**
>
>```
>输入：[2, 2, 3, 1]
>输出：1
>解释：注意，要求返回第三大的数，是指在所有不同数字中排第三大的数。
>此例中存在两个值为 2 的数，它们都排第二。在所有不同数字中排第三大的数为 1 。
>```

##### 分析(去重+排序)

可以通过先对数组去重, 然后排序的方式解决.

由于去重的耗时为 $O(n)$, 排序的耗时为 $O(n\log{n})$, 因此此种解法的耗时为 $O(n\log{n})$. 显然不是最优解

##### 分析(基于快速选择)

我们可以利用上述题目 [215. 数组中的第K个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/) 中的线性快速选择算法, 实现$O(n)$的快速选择. 不过这里需要对数组做一次去重, 然后根据去重后的数组长度调整目标索引.

具体的实现如下, 这里略去了`quick_select`的实现, 其具体实现见 [215. 数组中的第K个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/)

```python
class Solution:
 # quick_select(self, arr, lo, hi, target)
    
    def thirdMax(self, nums: List[int]) -> int:
        # 先对nums去重, 然后使用 线性快速选择算法
        nums = list(set(nums)) # O(n)
        n = len(nums)
        if n < 3:
            # 如果去重数组长度小于3, 则改为找最大值
            return max(nums)
        else:
            # 否则则找第三大的值
            target = n - 3 
         return self.quick_select(nums, 0, n-1, target)
```

不过显然`quick_select`的实现较为复杂, 用在这里有点"杀鸡用牛刀"

##### 分析(有限变量+遍历)

经典的**找数组次大值**的做法是使用两个变量 `a` 和 `b` 分别存储遍历过程中的`最大值`和`次大值`, 且有`a > b`。

假设当前遍历到的元素为 `x`, 当满足如下条件时，考虑更新 `a` 或者`b`:

- 若 `x > a` , 说明`最大值`需要被更新，同时原来的`最大值`沦为次大值, 即有 `b=a; a=x;`

- 若 `x = a`,  此时最大值和次大值都不需要更新;

- 若 `x < a`, 此时次大值可能需要更新, 进一步检查`x`和`b`的关系, 若 `x > b`, 则更新`b=x`

回到本题中, 我们使用`a`, `b`, `c`三个变量分别保存`最大值`, `次大值`, `第三大值`, 且 `a > b > c`

从头遍历数组, 假设当前遍历到的元素为`x`, 则此时的情况为:

- `x > a`, 说明最大值需要更新, 同时 之前的最大值变为新的次大值, 之前的次大值变为新的第三大值;
- `x < a and x > b`, 说明次大值需要更新, 之前的次大值变为新的第三大值;
- `x < b and x > c`, 说明第三大值需要更新

根据上面的分析, 初始化时`a`, `b`, `c`要初始化为一个足够小的值(`float('-inf')`).

通过这种方式遍历时, 不需要对数组去重, 直接遍历即可. 如果遍历结束, 第三大值依然是初始化时的取值, 则说明不存在第三大的值.

实现如下:

```python
class Solution:
    def thirdMax(self, nums: List[int]) -> int:
        n = len(nums)
        # 使用`a`, `b`, `c`三个变量分别保存`最大值`, `次大值`, `第三大值`,
        a = b = c = float("-inf")
        for x in nums:
            if x > a:
                a, b, c = x, a, b
            elif x < a and x > b:
                b, c = x, b
            elif x < b and x > c:
                c = x
        # 如果遍历结束, 第三大值依然是初始化时的取值, 则说明不存在第三大的值
        if c == float("-inf"):
            return a
        else:
            return c
```

#### [164. 最大间距](https://leetcode.cn/problems/maximum-gap/)

> 难度困难557
>
> 给定一个无序的数组 `nums`，返回 *数组在排序之后，相邻元素之间最大的差值* 。如果数组元素个数小于 2，则返回 `0` 。
>
> **您必须编写一个在「线性时间」内运行并使用「线性额外空间」的算法。**
>
> **示例 1:**
>
> ```
> 输入: nums = [3,6,9,1]
> 输出: 3
> 解释: 排序后的数组是 [1,3,6,9], 其中相邻元素 (3,6) 和 (6,9) 之间都存在最大差值 3。
> ```
>
> **提示:**
>
> - `1 <= nums.length <= 105`
> - `0 <= nums[i] <= 109`

##### 分析(基于桶排序思想)

假设数组长度为`N`, 且最小值为`min`, 最大值为`max`, 则排序后相邻两个元素的**最大差值**必然`>=ceiling((max-min)/(N-1))`!

我们将数组中的元素划分到若干个桶中, 令每个桶的容量为 `floor((max-min)/(N-1))`, 由于 `floor((max-min)/(N-1)) <ceiling((max-min)/(N-1))`, 因此**元素之间的最大差距一定不会出现在桶的内部, 而是出现在两个桶之间 !**

假设桶的容量为`BucketSize`, 则桶的数量即为 `floor((max-min)/BucketSize) + 1`, 带入计算后可以得到桶的数量约等于`N`.

则对于`i-th`桶(从0开始计数), 桶内元素的范围即为 `[min+i*BucketSize, min+(i+1)*BucketSize]`

我们遍历数组, 将每个元素放入其正确的桶中, 如果元素取值为`x`, 则其对应的桶号为 `floor((x-min)/BucketSize)`.

如前述, **元素之间的最大差距一定不会出现在桶的内部, 而是出现在两个桶之间 .** 因此我们只需要从头遍历桶(可能存在`空桶`, 遇到空桶则跳过), **计算当前桶的最大值和下一个桶的最小值之间的差值**(绝对值), 并记录最大的差值, 即为本题的解.

注意, *由于我们实际只需要查看每个桶内的最小值和最大值, 因此每个桶实际只需要保存两个值就可以了* (桶内最大值, 桶内最小值), 其他值可以忽略.

下图展示了计算过程:

<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230419112932.png" alt="image-20230419112932650" style="zoom:50%;" />

实现如下:

```python
import math
class Solution:
    def maximumGap(self, nums: List[int]) -> int:
        n = len(nums)
        if n < 2:
            # 如果数组元素个数小于 2，则返回 `0`
            return 0
        max_val = max(nums)
        min_val = min(nums)
        if max_val == min_val:
            # 数组内都是一样的数字
            return 0
        
        # 桶的容量, 这里要避免桶的大小为0
        bucket_size = max(1, math.floor((max_val-min_val)/(n-1)))
        # 桶的数量
        bucket_number = math.floor((max_val-min_val)/bucket_size) + 1
        
        # 初始化空桶, 由于我们实际只需要放入最大值和最小值, 
        # 因此这里每个桶就是一个元组(bucket_min, bucket_max)
        # 由于数组内取值大于0, 因此我们用-1初始化元组

        # 初始化bucket_number个桶, 每个桶实际是一个元组(bucket_min, bucket_max)
        buckets = [[-1,-1] for _ in range(bucket_number)]

        # 向桶内放入元素, 只放入最大值和最小值, 可能有空桶
        for x in nums:
            # 桶号为idx
            idx = math.floor((x-min_val)/bucket_size)
            if buckets[idx][0] < 0 or buckets[idx][0] > x:
                # 更新桶内最小值
                buckets[idx][0] = x
            if buckets[idx][1] < 0 or buckets[idx][1] < x:
                # 更新桶内最大值
                buckets[idx][1] = x

        # 遍历桶, 计算桶之间的差值(下一个桶的最小值 减去 当前桶的最大值)
        max_gap = 0
        cur = 0
        while cur < bucket_number-1:
            if buckets[cur][0] == -1:
                # 如果当前桶是空桶, 则前移i
                cur += 1
                continue
            next = cur + 1
            # 可能会有空桶
            while next < bucket_number and buckets[next][0] == -1:
                next += 1
            if next == bucket_number:
                return 0
            max_gap = max(max_gap, buckets[next][0] - buckets[cur][1])
            cur = next
        
        return max_gap
```

整个算法需要多次遍历数组($O(N)$), 以及遍历一遍桶, 由于桶的数量约等于N, 因此整个算法的时间复杂度为 $O(N)$

#### [220. 存在重复元素 III](https://leetcode.cn/problems/contains-duplicate-iii/)

> 难度困难688
>
> 给你一个整数数组 `nums` 和两个整数 `k` 和 `t` 。请你判断是否存在 **两个不同下标** `i` 和 `j`，使得 `abs(nums[i] - nums[j]) <= t` ，同时又满足 `abs(i - j) <= k` 。
>
> 如果存在则返回 `true`，不存在返回 `false`。
>
> **示例 1：**
>
> ```
>输入：nums = [1,2,3,1], k = 3, t = 0
> 输出：true
> ```

#### [148. 排序链表](https://leetcode.cn/problems/sort-list/)

> 难度中等1974
>
> 给你链表的头结点 `head` ，请将其按 **升序** 排列并返回 **排序后的链表** 。
>
> **示例 1：**
>
> <img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230419215345.jpeg" alt="img" style="zoom:33%;" />
>
> ```
> 输入：head = [4,2,1,3]
> 输出：[1,2,3,4]
> ```

##### 分析(基于**归并排序**)

时间复杂度为 $O(n\log{n})$ 的排序算法有 *归并排序, 堆排序, 快速排序*. 其中**最适合`链表`排序的算法是`归并排序`.**

归并排序基于分治法, 其有 自顶向下(递归)法 和 自底向上(迭代)法 两种, 其中递归法由于涉及到递归调用的栈空间, 所以空间复杂度为 $O(\log{n})$. 而迭代法的空间复杂度为 $O(1)$. 下面分别实现两种做法:

###### 自顶向下(递归)

对`链表`执行自顶向下的`归并排序`的过程为:

1. 找到链表的`中点`, 以中点为界, 将链表分为左右两个子链表. 要寻找一个链表的中点, 可以使用`快慢指针`([876. 链表的中间结点](https://leetcode-cn.com/problems/middle-of-the-linked-list/)): *快指针每次移动2步，慢指针每次移动1步，当快指针到达链表末尾时，慢指针指向的链表节点即为链表的中点*。
2. 对两个子链表分别`递归`排序.
3. 将两个排序后的子链表`合并`. 此时可以使用`双指针`合并两个有序链表([21. 合并两个有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/))

**递归退出的条件是待排序列表的长度为0或者1**, 此时可以视为是有序的.

具体的实现如下:

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def merge_sort(self, head: Optional[ListNode], tail: Optional[ListNode]) -> Optional[ListNode]:
        """
        对链表做 归并排序, 待排序链表的 头结点是head, 尾结点是tail
        注意这里的尾结点是链表待排序区域最后一个节点的下一个节点
        例如, 
        + 若链表为 a->b->c, 全部为待排序区域, 则头结尾为a, 尾结点为None(c的下一个节点)
        + 若链表为 a->b->c->d->e, 待排序区域为前三个节点, 则头结尾为a, 尾结点为d(c的下一个节点)
        """
        if not head:
            # 如果链表为空, 则返回空节点
            return head
        if head.next == tail:
            # 如果链表长度为1, 则直接返回 头结点
            # 注意此时需要将链表 "切开", 以便后面 "合并"
            head.next = None
            return head
        # 快慢指针找到头结点和尾结点之间的中间节点
        slow = fast = head
        while fast != tail and fast.next != tail:
            slow = slow.next
            fast = fast.next.next
        mid = slow
        # 对两个子链表分别递归排序, 并返回各自的头结点, 
        # 注意这里实际将中间节点分给了右侧的子链表
        # 如果待排序链表为 a->b->c->d->e, 则mid为c
        # 左侧区域为 a->b (head为a, tail为c)
        # 右侧区域为 c->d->e (head为c, tail为None)
        left = self.merge_sort(head, mid) 
        right = self.merge_sort(mid, tail)
        # 此时链表已经"切开", 因此只需要头结点, 不需要尾结点
        return self.merge(left, right)

    def merge(self, head1: Optional[ListNode], head2:Optional[ListNode]) -> Optional[ListNode]:
        """
        利用双指针合并两个有序链表
        """
        temp_node = ListNode()
        temp = temp_node
        while head1 and head2:
            if head1.val <= head2.val:
                temp.next = head1
                head1 = head1.next
            else:
                temp.next = head2
                head2 = head2.next
            temp = temp.next
        if head1:
            temp.next = head1
        else:
            temp.next = head2
        return temp_node.next

    def sortList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        return self.merge_sort(head, None)
```

###### 自底向上(迭代)

对链表执行自底向上的归并排序的过程为:

1. 将链表划分为若干个子链表, 初始时子链表的长度`subLen=1`(可以视为有序), 最后一个子链表的长度可以小于`subLen`;
2. 将相邻的两个`有序`子链表两两合并, 合并后得到若干个长度为`subLen*2`的`有序`子链表. 此时可以使用`双指针`合并两个有序链表([21. 合并两个有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/))
3. 更新`subLen = subLen * 2`, 重复第2步, 对更长的子链表合并, 直到合并后有序链表的长度等于整个链表长度为止

实现如下:

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def merge(self, head1: Optional[ListNode], head2:Optional[ListNode]) -> Optional[ListNode]:
        """
        利用双指针合并两个有序链表
        """
        temp_node = ListNode()
        temp = temp_node
        while head1 and head2:
            if head1.val <= head2.val:
                temp.next = head1
                head1 = head1.next
            else:
                temp.next = head2
                head2 = head2.next
            temp = temp.next
        if head1:
            temp.next = head1
        else:
            temp.next = head2
        return temp_node.next
    
    def merge_sort(self, head: ListNode, length: int) -> ListNode:
        """
        head: 待排序的链表头结点
        length: 待排序的链表的长度
        """
        # 初始化子链表长度
        sub_len = 1
        # 循环, 直到子链表长度大于等于链表长度为止
        while sub_len < length:
            # 将链表切分为 若干个子链表, 每个子链表长度为subLen
            curr = head
            # 定义一个临时空节点, 用来连接合并后的链表
            dummy_head = ListNode(-1)
            # head也指向这个临时空节点(结束后head的next节点就是真正的头结点)
            head = dummy_head
            while curr:
                # 每次切割得到两个子链表, 然后合并; 然后继续向后切分
                # 切割第一个子链表:
                head1 = curr
                # 将curr指针前移subLen-1, 得到一个子链表
                # (最后一个子链表长度可能小于subLen)
                for _ in range(sub_len-1):
                    if curr.next:
                        curr = curr.next
                    else:
                        break
                # 此时head1指向当前子链表头结点, curr指向当前子链表最后一个节点
                # 此时需要 切开 当前子链表和后序节点的连接
                head2 = curr.next # 此时curr.next可能为空
                curr.next = None # "切开"
                # 切割第二个子链表:
                curr = head2 # 此时curr可能为空
                # 将curr指针前移subLen-1, 得到另一个子链表
                # (最后一个子链表长度可能小于subLen)
                for _ in range(sub_len-1):
                    if curr and curr.next: # 此时curr可能为空
                        curr = curr.next
                    else:
                        break
                # 此时head2指向当前子链表头结点, curr指向当前子链表最后一个节点
                # 此时需要 切开 当前子链表和后序节点的连接
                # 此时curr可能为空
                if curr:
                    temp = curr.next
                    curr.next = None # "切开"
                else:
                    temp = None
                # 至此我们得到了两个"切开"的有序子链表, 
                # 头结点分别为 head1, head2, 执行合并
                merged = self.merge(head1, head2)
                # 合并后的链表应该连接到一个新的链表上
                dummy_head.next = merged
                # 然后后移临时头结点, 准备连接下一次合并后的链表
                while dummy_head.next:
                    dummy_head = dummy_head.next
                # 将curr指针指向切开后的下一个节点
                curr = temp
            # "一层切分-合并"结束后, 需要更新子链表长度
            sub_len *= 2
            # 一开始head指向临时节点, 现在需要后移一个节点才是真正的头结点
            head = head.next
        
        return head
        
    def sortList(self, head: ListNode) -> ListNode:
        if not head:
            # 空节点直接返回
            return head
        # 自底向上归并首先要知道整个链表的长度
        length = 0
        node = head
        while node:
            length += 1
            node = node.next
        
        return self.merge_sort(head, length)
```

#### [23. 合并 K 个升序链表](https://leetcode.cn/problems/merge-k-sorted-lists/)

> 难度困难2410
>
> 给你一个链表数组，每个链表都已经按升序排列。
>
> 请你将所有链表合并到一个升序链表中，返回合并后的链表。
>
> **示例 1：**
>
> ```
> 输入：lists = [[1,4,5],[1,3,4],[2,6]]
> 输出：[1,1,2,3,4,4,5,6]
> 解释：链表数组如下：
> [
> 1->4->5,
> 1->3->4,
> 2->6
> ]
> 将它们合并到一个有序链表中得到。
> 1->1->2->3->4->4->5->6
> ```
>

> 本题是 [21. 合并两个有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/) 的进阶, 在 [21. 合并两个有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/) 中, 我们使用`双指针`法, 可以在 $O(n)$ 时间复杂度和 $O(1)$ 空间复杂度下完成两个有序链表的合并.
>
> 但是本题是`k`个链表, 一种**朴素**的想法就是两两使用上面的双指针合并法, 每次都消去一个链表, 直到所有链表合并完成. 这种算法的时间复杂度比较高.
>
> 假设每个链表的平均长度为`n`, 我们将合并视为`k`次, 则第一次(第一次实际没有合并)两两合并后的链表长度为`n`, 第二次合并后长度为`2n`, 第`i`次合并为长度为`i*n`. 则第`i`次合并的时间复杂度为 $O(n+(i-1)n)=O(i\cdot{n})$. 那么总的时间复杂度为:
> $$
> O(\sum^k_{i=1}{i\cdot{n}}) = O(\frac{(1+k)\cdot{k}}{2}\cdot{n}) = O(k^2\cdot{n})
> $$
> 空间复杂度为 $O(1)$.
>
> 下面我们寻找比上述复杂度更好的算法

##### 分析(基于**堆排序**)

利用最小堆的性质完成`k`个升序链表的合并.

首先初始化一个最小堆, 将`k`个链表的头节点压入堆. 此时堆顶元素就是所有链表中的最小节点.

然后依次弹出堆顶元素, 如果堆顶元素对应节点在原链表中还有后序节点, 则继续将该后序节点压入堆, 然后将弹出的堆顶元素保存到新的链表中. 重复上述操作, 直到堆为空为止, 此时新的链表即为有序链表.

> 注意: Python中的`heapq`是最小堆, 而且不支持直接通过`key`关键字指定`比较器`, 因此下面用一个封装类封装了链表节点, 并实现了`__lt__`方法.

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
import heapq
class ListNodeWrapper:
    def __init__(self, node: ListNode):
        self.node = node
    def __lt__(self, other):
        return self.node.val < other.node.val

class Solution:
    def mergeKLists(self, lists: List[Optional[ListNode]]) -> Optional[ListNode]:
        if not lists:
            return None
        
        min_heap = []
        # 初始化堆
        for head in lists:
            # 将每个链表的头节点压入堆
            if head:
                heapq.heappush(min_heap, ListNodeWrapper(head))
        # 由于每个链表都是升序的, 因此当前堆顶必然是最小的元素
        # 初始化一个临时空节点
        dummy_head = ListNode(-1)
        p = dummy_head
        while min_heap:
            # 弹出堆顶元素,
            # 由于原始的链表是升序的, 因此堆顶必然是当前堆内和剩余元素的最小值
            node = heapq.heappop(min_heap).node
            p.next = node
            p = p.next
            # 如果堆顶节点还有后序节点, 则压入堆
            if node.next:
                heapq.heappush(min_heap, ListNodeWrapper(node.next))
        return dummy_head.next
```

时间复杂度: 堆内节点数量不会超过`k`个, 故堆的插入和弹出的时间复杂度都为 $O(\log{k})$ ; 所有链表合并后最多有`k*n`个节点, 每个节点都需要做一次入堆和弹出, 因此总的时间复杂度为 $O(kn\log{k})$ , 要好于朴素算法.

空间复杂度: 堆内节点数量不会超过`k`个, 故空间复杂度为 $O(k)$ .

##### 分析(基于归并排序的思想)

我们不再从头两两合并, 而是将`k`组链表两两配对合并, 第一轮合并后得到`k/2`个链表, 然后继续下一轮合并, 直到只剩一个链表为止. 每次合并时都使用双指针法.  

其实这个思路就是`自下而上(迭代)的归并排序`, 只不过这里开始时不是从长度为`1`的子链表开始合并了, 等于简化了"分割"的环节. 看下面的示意图更容易理解这个算法和归并排序的关系.

<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230420110917.png" alt="img" style="zoom:50%;" />

具体的实现如下:

```
```

复杂度分析: 假设链表的平均长度为`n`, 则第`1`遍合并后链表长度为`2n`, 链表数量为`k/2`; 第二遍合并后链表长度为`2*n*2=4*n`, 链表数量变为`k/4`. 则第`i`遍合并后链表长度为 $2^i n$, 链表数量变为 $k/2^i$, 此时一次`合并(merge)`操作的时间复杂度为 $O(2^i\cdot{n})$, 共执行了 $k/2^i$ 次.  重复合并直到只剩一个链表为止, 即此时$2^i=k$, 也就是总共执行 $\log_2{k}$ 遍合并.

故总的时间复杂度为 $O(\sum^{\log{k}}_{i=1}{k/2^i \cdot 2^in}) = kn\sum^{\log{k}}_{i=1}{1} = kn\log{k}$ , 优于朴素算法.

空间复杂度: 如果使用迭代的方式实现, 则空间复杂度为 $O(1)$, 如果使用递归实现, 则空间复杂度为 $O(\log{k})$.

#### [剑指 Offer 51. 数组中的逆序对](https://leetcode.cn/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

> 难度困难995
>
> 在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组，求出这个数组中的逆序对的总数。
>
> **示例 1:**
>
> ```
> 输入: [7,5,6,4]
> 输出: 5
> ```
>

#### [295. 数据流的中位数](https://leetcode.cn/problems/find-median-from-data-stream/)

> 难度困难817
>
> **中位数**是有序整数列表中的中间值。如果列表的大小是偶数，则没有中间值，中位数是两个中间值的平均值。
>
> - 例如 `arr = [2,3,4]` 的中位数是 `3` 。
> - 例如 `arr = [2,3]` 的中位数是 `(2 + 3) / 2 = 2.5` 。
>
> 实现 MedianFinder 类:
>
> - `MedianFinder()`初始化 `MedianFinder` 对象。
> - `void addNum(int num)` 将数据流中的整数 `num` 添加到数据结构中。
> - `double findMedian()` 返回到目前为止所有元素的中位数。与实际答案相差 `10-5` 以内的答案将被接受。
>
> **示例 1：**
>
> ```
> 输入
> ["MedianFinder", "addNum", "addNum", "findMedian", "addNum", "findMedian"]
> [[], [1], [2], [], [3], []]
> 输出
> [null, null, null, 1.5, null, 2.0]
> 
> 解释
> MedianFinder medianFinder = new MedianFinder();
> medianFinder.addNum(1);    // arr = [1]
> medianFinder.addNum(2);    // arr = [1, 2]
> medianFinder.findMedian(); // 返回 1.5 ((1 + 2) / 2)
> medianFinder.addNum(3);    // arr[1, 2, 3]
> medianFinder.findMedian(); // return 2.0
> ```

##### 分析(基于**堆排序**)

由于本题的输入是数据流，数据会不断涌入中，因此也就面临着需要多次动态调整以获得中位数。 因此实现的数据结构需要既需要快速找到中位数，也需要做到快速调整。

容易想到利用自平衡二叉搜索树(如AVL树)，AVL树的插入耗时为$O(\log{n})$, 且其查找中位数的耗时为$O(\log{n})$, 实现相对高效, 但是AVL树的代码编写相对比较困难.

> AVL树的中序深度优先遍历得到的是一个升序序列, 假如当前树中有$n$个节点, 则只需要遍历到第$n/2$个节点即可获得中位数, 此操作复杂度为 $O(\log{n})$

由于本题只需要获得中间数，我们可以使用两个堆来模拟二叉搜索树. 左侧堆`left_heap`中的元素全部小于等于当前中位数, 其使用`最大堆`实现; 右侧堆`right_heap`中的元素全部大于当前中位数, 其使用`最小堆`实现. 同时记录当前累计添加的元素数量`n`. **同时为了保证得到的是中位数, 左右两个堆的长度差不能超过1.**

**当查找中位数时**:

- 如果`n`是奇数, 则此时`left_heap`的长度应该比`right_heap`大一, 此时中位数就是左侧堆`left_heap`的堆顶元素.
- 如果`n`是偶数, 则此时两侧堆长度应该相等, 此时中位数就是左右两个堆的堆顶元素的平均值.

**当插入新元素`num`时**:

- 初始时(累计元素数量为0), 将`nums`添加到左侧堆`left_heap`中 (这里实际将左侧堆堆顶作为当前的中位数, 之后每次添加新元素都需要和左侧堆堆顶比较);

- 如果`num`小于等于左侧堆`left_heap`堆顶元素, 则说明`num`小于等于中位数, 我们将`num`添加到`left_heap`中. 此时如果`left_heap`和`right_heap`的长度差大于一, 则需要将`left_heap`的堆顶移动到`right_heap`中.
- 如果`num`大于左侧堆`left_heap`堆顶元素, 则说明`num`大于中位数, 我们将`num`添加到`right_heap`中. 此时如果`right_heap`的长度比`left_heap`的长度大(*注意这里不是大于1才移动, 而是一旦大于就移动*), 则需要将`right_heap`的堆顶移动到`left_heap`中.

 上述方式实现的难度相对AVL树较低, 且复杂度相近.

具体的实现如下:

```python
import heapq
class MedianFinder:

    def __init__(self):
        self.count = 0
        self.left_heap = []
        self.right_heap = []


    def addNum(self, num: int) -> None:
        if self.count == 0:
            # 最大堆
            heapq.heappush(self.left_heap, -1 * num)
        else:
            left_top = -1 * self.left_heap[0]
            if num <= left_top:
                # 最大堆
                heapq.heappush(self.left_heap, -1 * num)
                if len(self.left_heap) - len(self.right_heap) > 1:
                    left_top = -1 * heapq.heappop(self.left_heap)
                    heapq.heappush(self.right_heap, left_top)
            else:
                # 最小堆
                heapq.heappush(self.right_heap, num)
                if len(self.right_heap) > len(self.left_heap):
                    right_top = heapq.heappop(self.right_heap)
                    heapq.heappush(self.left_heap, -1 * right_top)
        self.count += 1

    def findMedian(self) -> float:
        if len(self.left_heap) > len(self.right_heap):
            return -1 * self.left_heap[0]
        else:
            return (-1 * self.left_heap[0] + self.right_heap[0]) / 2
```

##### 进阶

进阶一:
如果数据流中所有整数都在 0 到 100 范围内，那么我们可以利用计数排序统计每一类数的数量，并使用双指针维护中位数。

进阶二:
如果数据流中 99% 的整数都在 0 到 100 范围内，那么我们依然利用计数排序统计每一类数的数量，并使用双指针维护中位数。对于超出范围的数，我们可以单独进行处理，建立两个数组，分别记录小于 0 的部分的数的数量和大于 100 的部分的数的数量即可。当小部分时间，中位数不落在区间 [0,100] 中时，我们在对应的数组中暴力检查即可。

#### [1122. 数组的相对排序](https://leetcode.cn/problems/relative-sort-array/)

> 难度简单
>
> 给你两个数组，`arr1` 和 `arr2`，`arr2` 中的元素各不相同，`arr2` 中的每个元素都出现在 `arr1` 中。
>
> 对 `arr1` 中的元素进行排序，使 `arr1` 中项的相对顺序和 `arr2` 中的相对顺序相同。未在 `arr2` 中出现过的元素需要按照升序放在 `arr1` 的末尾。
>
> **示例 1：**
>
> ```
> 输入：arr1 = [2,3,1,3,2,4,6,7,9,2,19], arr2 = [2,1,4,3,9,6]
> 输出：[2,2,2,1,4,3,3,9,6,7,19]
> ```
>
> **提示：**
>
> ```
> 1 <= arr1.length, arr2.length <= 1000
> 0 <= arr1[i], arr2[i] <= 1000
> arr2 中的元素 arr2[i]  各不相同 
> arr2 中的每个元素 arr2[i] 都出现在 arr1 中
> ```

#### [274. H 指数](https://leetcode.cn/problems/h-index/)

难度中等304

给你一个整数数组 `citations` ，其中 `citations[i]` 表示研究者的第 `i` 篇论文被引用的次数。计算并返回该研究者的 **`h` 指数**。

根据维基百科上 [h 指数的定义](https://baike.baidu.com/item/h-index/3991452?fr=aladdin)：h 代表“高引用次数”，一名科研人员的 `h`**指数**是指他（她）的 （`n` 篇论文中）**总共**有 `h` 篇论文分别被引用了**至少** `h` 次。且其余的 *`n - h`* 篇论文每篇被引用次数 **不超过** *`h`* 次。

如果 `h` 有多种可能的值，**`h` 指数** 是其中最大的那个。

**示例 1：**

```
输入：citations = [3,0,6,1,5]
输出：3 
解释：给定数组表示研究者总共有 5 篇论文，每篇论文相应的被引用了 3, 0, 6, 1, 5 次。
     由于研究者有 3 篇论文每篇 至少 被引用了 3 次，其余两篇论文每篇被引用 不多于 3 次，所以她的 h 指数是 3。
```

#### [561. 数组拆分](https://leetcode.cn/problems/array-partition/)

难度简单329

给定长度为 `2n` 的整数数组 `nums` ，你的任务是将这些数分成 `n` 对, 例如 `(a1, b1), (a2, b2), ..., (an, bn)` ，使得从 `1` 到 `n` 的 `min(ai, bi)` 总和最大。

返回该 **最大总和** 。

**示例 1：**

```
输入：nums = [1,4,3,2]
输出：4
解释：所有可能的分法（忽略元素顺序）为：
1. (1, 4), (2, 3) -> min(1, 4) + min(2, 3) = 1 + 2 = 3
2. (1, 3), (2, 4) -> min(1, 3) + min(2, 4) = 1 + 2 = 3
3. (1, 2), (3, 4) -> min(1, 2) + min(3, 4) = 1 + 3 = 4
所以最大总和为 4
```
