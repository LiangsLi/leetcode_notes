# 字典树 Trie

> A **trie**, also called **digital tree** or **prefix tree**, is a kind of [search tree](https://en.wikipedia.org/wiki/Search_tree)—an ordered [tree](https://en.wikipedia.org/wiki/Tree_(data_structure)) [data structure](https://en.wikipedia.org/wiki/Data_structure) used to store a [dynamic set](https://en.wikipedia.org/wiki/Set_(abstract_data_type)) or [associative array](https://en.wikipedia.org/wiki/Associative_array) where the keys are usually **string**.
>
> Trie(又称 字典树) 是一类 搜索树, 其可以用来保存字符串类型键值的动态集合或者映射字典.
>
> Unlike a [binary search tree](https://en.wikipedia.org/wiki/Binary_search_tree), no node in the tree stores the key associated with that node; instead, its position in the tree defines the key with which it is associated; i.e., the value of the key is distributed across the structure. 
>
> 与二叉搜索树不同, trie 树中的结点不直接保存其关联的值, 相反, 其使用结点在树中的位置来表示结点关联的值.
>
> All the **descendants** of a node have a common [prefix](https://en.wikipedia.org/wiki/Prefix) of the string associated with that node, and the root is associated with the [empty string](https://en.wikipedia.org/wiki/Empty_string). 
>
> 一个结点的所有后继节点都和该结点拥有同样的前缀, trie 的根节点为空字符串.
>
> A **radix tree** (also **radix trie** or **compact prefix tree**) is a [data structure](https://en.wikipedia.org/wiki/Data_structure) that represents a space-optimized [trie](https://en.wikipedia.org/wiki/Trie) (prefix tree) in which each node that is the only child is merged with its parent.
>
> 基数树(又称 压缩前缀树)是一种空间优化的 Trie 树.

对应的 Python 实现:

+ [pytries/marisa-trie: Static memory-efficient Trie-like structures for Python (2.x and 3.x) based on marisa-trie C++ library.](https://github.com/pytries/marisa-trie)
+ [pytries/datrie: Fast, efficiently stored Trie for Python. Uses libdatrie.](https://github.com/pytries/datrie)
+ [google/pygtrie: Python library implementing a trie data structure.](https://github.com/google/pygtrie)

trie 树的名称来自于单词 "*retrieval*",  可以读作 "tree" 或者 "try".



假设有 5 个字符串，它们分别是： him 、 her 、 cat 、 no 、 nova。则可以构造一个字典树如下:

<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20210113163827.jpg" alt="img" style="zoom: 50%;" />

这样就方便了字符串的插入和查找, 观察上图, 可以看出, 字典树有三个特点:

+ 根节点不包含字符，其他节点包含一个字符。
+ 从根节点到某一节点经过的字符连接起来构成一个字符串。
+  一个字符串与 Trie 树中的一条路径对应。
+  在实现过程中，会在叶节点中设置一个标志，用来表示该节点是否是一个字符串的结尾，本例中用青色填充进行标记。

<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20230421161450.png" alt="image-20230421161443558" style="zoom:50%;" />

#### [208. 实现 Trie (前缀树)](https://leetcode.cn/problems/implement-trie-prefix-tree/)

> 难度中等1437
>
> **[Trie](https://baike.baidu.com/item/字典树/9825209?fr=aladdin)**（发音类似 "try"）或者说 **前缀树** 是一种树形数据结构，用于高效地存储和检索字符串数据集中的键。这一数据结构有相当多的应用情景，例如自动补完和拼写检查。
>
> 请你实现 Trie 类：
>
> - `Trie()` 初始化前缀树对象。
> - `void insert(String word)` 向前缀树中插入字符串 `word` 。
> - `boolean search(String word)` 如果字符串 `word` 在前缀树中，返回 `true`（即，在检索之前已经插入）；否则，返回 `false` 。
> - `boolean startsWith(String prefix)` 如果之前已经插入的字符串 `word` 的前缀之一为 `prefix` ，返回 `true` ；否则，返回 `false` 。
>
> **示例**:
>
> ```
> 输入
> ["Trie", "insert", "search", "search", "startsWith", "insert", "search"]
> [[], ["apple"], ["apple"], ["app"], ["app"], ["app"], ["app"]]
> 输出
> [null, null, true, false, true, null, true]
> 
> 解释
> Trie trie = new Trie();
> trie.insert("apple");
> trie.search("apple");   // 返回 True
> trie.search("app");     // 返回 False
> trie.startsWith("app"); // 返回 True
> trie.insert("app");
> trie.search("app");     // 返回 True
> ```
>
> **提示**:
>
> ```
> 1 <= word.length, prefix.length <= 2000
> word 和 prefix 仅由小写英文字母组成
> ```



实现如下:

```python
class Trie:
    def __init__(self):
        # 由于本题仅处理英文(无数字等其他字符),且所有单词都是小写, 
        # 因此子节点最多有26个, 代表26个字母
        self.children = [None] * 26
        self.isEnd = False # 表示是否是字符串的结尾
    
    def insert(self, word: str) -> None:
        """ 插入字符串
        从字典树的根开始，插入字符串。对于当前字符对应的子节点，有两种情况：
        + 子节点存在: 沿着指针移动到子节点，继续处理下一个字符
        + 子节点不存在: 创建一个新的子节点, 记录在children数组的对应位置上，然后沿着指针移动到子节点，继续搜索下一个字符
        重复以上步骤，直到处理字符串的最后一个字符，然后将当前节点标记为字符串的结尾(isEnd=True)
        """
        node = self
        for ch in word:
            # 先获取当前字符对应的子节点索引
            ch = ord(ch) - ord("a")
            if not node.children[ch]:
                node.children[ch] = Trie()
            node = node.children[ch]
        node.isEnd = True
    
    def searchPrefix(self, prefix: str) -> "Trie":
        """ 查找前缀
        从字典树的根开始，查找前缀。对于当前字符对应的子节点，有两种情况：
        + 子节点存在: 沿着指针移动到子节点，继续搜索下一个字符。
        + 子节点不存在: 说明字典树中不包含该前缀，返回空节点。
        重复以上步骤，直到返回空指针或搜索完前缀的最后一个字符
        若搜索到了前缀的末尾，就说明字典树中存在该前缀, 返回对应的节点。
        此外，若前缀末尾对应节点的 isEnd 为真，则说明字典树中存在该字符串。
        """
        node = self
        for ch in prefix: # 逐个字符处理
            ch = ord(ch) - ord("a")
            if not node.children[ch]:
                return None
            node = node.children[ch]
        return node
    
    def search(self, word: str) -> bool:
        """ 查找单词
        类似查找前缀, 将单词视为前缀进行查找, 
        如果返回的节点的 isEnd = True, 则说明存在当前单词, 返回True, 否则返回False
        """
        node = self.searchPrefix(word)
        return node is not None and node.isEnd
    
    def startsWith(self, prefix: str) -> bool:
        """如果之前已经插入的字符串 word 的前缀之一为 prefix ，返回 true ；否则，返回 false"""
        return self.searchPrefix(prefix) is not None
```

> 也可以使用字典实现一个简化版本的字典树, 此时每一个节点都是一个Dict, 而不是Trie节点.

#### [648. 单词替换](https://leetcode.cn/problems/replace-words/)

> 难度中等276
>
> 在英语中，我们有一个叫做 `词根`(root) 的概念，可以词根**后面**添加其他一些词组成另一个较长的单词——我们称这个词为 `继承词`(successor)。例如，词根`an`，跟随着单词 `other`(其他)，可以形成新的单词 `another`(另一个)。
>
> 现在，给定一个由许多**词根**组成的词典 `dictionary` 和一个用空格分隔单词形成的句子 `sentence`。你需要将句子中的所有**继承词**用**词根**替换掉。如果**继承词**有许多可以形成它的**词根**，则用**最短**的词根替换它。
>
> 你需要输出替换之后的句子。
>
>  **示例 1：**
>
> ```
> 输入：dictionary = ["cat","bat","rat"], sentence = "the cattle was rattled by the battery"
> 输出："the cat was rat by the bat"
> ```
>
> **示例 2：**
>
> ```
> 输入：dictionary = ["a","b","c"], sentence = "aadsfasf absbs bbab cadsfafs"
> 输出："a a b c"
> ```
>
> **提示**:
>
> ```
> 1 <= dictionary.length <= 1000
> 1 <= dictionary[i].length <= 100
> dictionary[i] 仅由小写字母组成。
> 1 <= sentence.length <= 10^6
> sentence 仅由小写字母和空格组成。
> ```



> 这里可以用上面的完整的字典树实现, 也可以使用字典实现一个简化版本的字典树, 此时每一个节点都是一个Dict, 而不是Trie节点.

我们用 dictionary 中所有词根构建一棵字典树，并用特殊符号标记结尾。在搜索前缀时，只需在字典树上搜索出一条最短的前缀路径即可。

```python
class Solution:
    def replaceWords(self, dictionary: List[str], sentence: str) -> str:
        # 构建字典树
        trie = {}
        for word in dictionary:
            curNode  = trie
            for ch in word:
                if ch not in curNode:
                    curNode[ch] = {}
                curNode = curNode[ch]
            curNode["#"] = {}
        
        words = sentence.split(" ")
        for i, w in enumerate(words):
            curNode = trie
            for j, c in enumerate(w):
                if "#" in curNode:
                    words[i] = w[:j]
                    break
                if c not in curNode:
                    break
                curNode = curNode[c]
        return " ".join(words) 
```

