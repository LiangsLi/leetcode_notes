> [树形 DP - OI Wiki](https://oi-wiki.org/dp/tree/)

<!--ts-->
         * [<a href="https://leetcode.cn/problems/sum-of-distances-in-tree/" rel="nofollow">834. 树中距离之和</a>](树形DP.md#834-树中距离之和)
         * [<a href="https://leetcode.cn/problems/house-robber-iii/" rel="nofollow">337. 打家劫舍 III</a>](树形DP.md#337-打家劫舍-iii)

<!-- Created by https://github.com/ekalinin/github-markdown-toc -->
<!-- Added by: huayongli, at: Mon Jan 15 20:06:18 CST 2024 -->

<!--te-->

#### [834. 树中距离之和](https://leetcode.cn/problems/sum-of-distances-in-tree/)

> 难度困难379
>
> 给定一个无向、连通的树。树中有 `n` 个标记为 `0...n-1` 的节点以及 `n-1` 条边 。
>
> 给定整数 `n` 和数组 `edges` ， `edges[i] = [ai, bi]`表示树中的节点 `ai` 和 `bi` 之间有一条边。
>
> 返回长度为 `n` 的数组 `answer` ，其中 `answer[i]` 是树中第 `i` 个节点与所有其他节点之间的距离之和。

#### [337. 打家劫舍 III](https://leetcode.cn/problems/house-robber-iii/)

难度中等1637收藏分享切换为英文接收动态反馈

小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为 `root` 。

除了 `root` 之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果 **两个直接相连的房子在同一天晚上被打劫** ，房屋将自动报警。

给定二叉树的 `root` 。返回 ***在不触动警报的情况下** ，小偷能够盗取的最高金额* 。
