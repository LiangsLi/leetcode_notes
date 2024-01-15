# 卡塔兰数(Catalan Number)

[toc]

### 定义

**卡塔兰数**是[组合数学](https://zh.wikipedia.org/wiki/組合數學)中一个常在各种计数问题中出现的自然数[数列](https://zh.wikipedia.org/wiki/数列), 其经常出现在 递归定义的结构体 中, 例如二叉树.

卡塔兰数的一般项公式为:
$$
C_{n} = \frac{1}{n+1}\tbinom{2n}{n} = \frac{(2n)!}{(n+1)!n!}
$$
递推公式为:
$$
C_0 = 1 \quad and \quad C_{n+1}=\frac{2(2n+1)}{n+2}C_{n}
$$

The first Catalan numbers for *n* = 0, 1, 2, 3, ... are
$$
1, 1, 2, 5, 14, 42, 132, 429, 1430, 4862, 16796, 58786,\dots
$$

### 应用

> [「算法入门笔记」卡特兰数 - 知乎](https://zhuanlan.zhihu.com/p/97619085)

以 $C_{3} = 5$ 为例, 卡塔兰数是一系列组合计数问题的通解, 例如:

+ 令`n`为左括号和右括号的数量, 任意组合`n`个左/右括号, 则其中能正确匹配的情况数量等于卡塔兰数, 例如, `n=3`时, $C_{3}=5$ :

  ```
  ((()))     ()(())     ()()()     (())()     (()())
  ```

+ `n` 个元素进栈序列为：`1，2，3，4，...，n`，则有多少种出栈序列. 答案也是卡塔兰数 $C_n$

### leetcode 题目

+ [96. 不同的二叉搜索树 - 力扣（LeetCode）](https://leetcode.cn/problems/unique-binary-search-trees/)

+
