---
title: 腾讯2024暑期实习笔试
tags: [Tencent]
categories: OA
cover: images/Algorithm/OA/tencent/tencent_cover.jpg
excerpt: 2024年3月31日20:00-22:00实习生在线笔试
---
<!-- toc -->

# Q1 小红的图上染色

## 题目描述
小红拿到了一个无向图，其中一些边被染成了红色，小红定义一个点为”好点“，当且仅当这个点的所有邻边都是红边。现在请你求出这个无向图”好点“的数量

注意：如果一个节点没有任何邻边，那么它也是好点

## 输入描述
第一行输入两个正整数$m$,$n$，代表节点的数量和边的数量。

接下来的m行，每行输入两个正整数$u$, $v$和一个字符chr，代表节点u和v有一条边连接，如果chr为'R'，代表这条边被染红；'W'代表未被染色

$1 <= n, m <= 10^5$

$1 <= u, v <= n$

## 输出描述
一个整数，代表“好点”的数量

## 示例1
输入
```
4 4
1 2 R
2 3 W
3 4 W
1 4 R
```

输出
```
1
```

说明：
只有1号节点是好点

## 解题思路
无向图使用邻接表构建出邻接表，再构建一个node的hashmap储存红边数量，最后判断邻接表长度是否等于红边数量。

```python
# edge: [["1", "2", "R"], ["2", "3", "W"], ["3", "4", "W"], ["1", "4", "R"]]
def build_graph_and_red_edges(edges: List[List[str]]):
    # 用于储存图
    graph = defaultdict(list)
    # 用于储存一个node的红边数量
    red = defaultdict(int)

    for u, v, color in edges:
        graph[int(u)].append(v)
        graph[int(v)].append(u)
        if color == "R":
            red[int(u)] += 1
            red[int(v)] += 1
    
    return graph, red

def count_good_nodes(graph, red, n):
    res = 0

    for node in range(1, n+1):
        if len(graph[node]) == red[node]:
            res += 1
    
    return res
```
# Q2 小红的链表破裂

## 题目描述
小红拿到了一个链表。她准备将这个链表断裂成两个链表，再拼接到一起，使得链表从头节点到尾部升序。你能帮小红判断是否达成目的吗？

给定的为一个链表数组，你需要对于数组中每个链表进行一次“是”或“否”的回答，并返回布尔数组。

每个链表的长度不小于2，且每个链表中不包含两个相等的元素。所有链表的长度之和保证不超过$10^5$

## 示例1
输入：
```
[{1, 2, 3}, {2, 3, 1}, {3, 2, 1}]
```

输出：
```
[true, false, true]
```

说明：

第三个链表无论怎么操作都不满足条件

## 解题思路
只有三种情况：

1. 升序
2. 先升序后降序
3. 先降序后升序

才能满足断裂后拼接在一起时满足升序的

```python
def check_sorted_linked_lists(arr):
    results = []
    # 由于我不知道原题的输入长什么样
    # 暂且按照数组来进行代码处理
    for lst in arr:
        
        # 找到链表(数组)中的最大值和最小值的位置
        min_index = lst.index(min(lst))
        max_index = lst.index(max(lst))
        # 判断三种情况
        # 1. 升序
        # 2. 先升序后降序
        # 3. 先降序后升序
        if sorted(lst[:min_index+1]) == lst[:min_index+1] and sorted(lst[max_index:]) == lst[max_index:]:
            results.append(True)
        else:
            results.append(False)
    return results
```

# Q3 小红的连通图

## 题目描述
小红拿到了一个有n个节点的无向图，这个图初始并不是连通图。

现在小红想知道，添加恰好一条边使得这个图连通，有多少种不同的加边方案？

## 输入描述
第一行输入两个正整数$n$, $m$，用空格隔开。

接下来的m行，每行输入两个正整数$u$, $v$，代表节点u和节点v之间有一条边连接。

$1 <= n, m <= 10^5$
$1 <= u, v <= n$

保证给出的图示不连通的。

## 输出描述
一个整数，代表加边的方案数

## 解题思路

我不会，让我想想...

# Q4 小红的数组分割

## 题目内容
小红拿到了一个数组，她准备将数组分成k段，使得每段内部按位异或之后，再全部求和，小红希望最终这个和尽可能大，你能帮帮她吗（不能）

## 输入描述
输入包含两行，第一行两个正整数$n$, $k$，$1 <= k <= n <= 400$，分别表示数组的长度和要分的段数。

第二行$n$个整数$a_i$ ($0 <= a_i <= 10^9$)，表示数组a的元素。

## 输出描述
输出一个正整数，表示最终的最大和

## 示例1
输入：
```
6 2
1 1 1 2 3 4
```
输出：
```
10
```
解释说明：

小红将数组分为了[1, 4], [5, 6]两个区间，得分分别为：$1 \oplus 1 \oplus 1 \oplus 2 = 3$ 和 $3 \oplus 4 = 7$，总得分为10。可以证明不存在比10更优的分割方案。

## 解题思路：
1. 采用动态规划的方法，用dp table记录当前位置将数组分成若干段的最大异或和，我们尝试所有可能的分段方法，更新dp table以找到最大的异或和。
2. 利用异或运算的逆运算是其本身，可以使用异或前缀和的方法快速计算任何一段异或和

```python
from typing import List

def max_xor(nums: List[int], k: int):
    # acquire the length of the array
    n = len(nums)

    # calculate pre-sum of xor
    pre_sum_xor = [0] * n
    pre_sum_xor[0] = nums[0]

    for i in range(1, n):
        pre_sum_xor[i] = pre_sum_xor[i-1] ^ nums[i]

    dp = [[0] * (k+1) for _ in range(n)]

    # dp[i][j] 表示index为i的元素分成j段的最大异或和
    for i in range(n):
        for j in range(1, k+1):
            # 只有一段的时候，直接计算从头到当前位置的异或和
            if j == 1:
                dp[i][j] = pre_sum_xor[i]
            # 多于一段的时候
            # 最大值 = ”前段异或和 + 前面部分“ 与 ”当前“ 最大值
            else:
                # 尝试不同的分段点
                for m in range(j, i):
                    dp[i][j] = max(dp[i][j], dp[m][j-1] + (pre_sum_xor[i] ^ pre_sum_xor[m]))
            
    return dp[n-1][k];
```

# Q5 小红的tencent矩阵

## 题目描述
小红拿到了一个字符矩阵，她可以从任意一个地点除法，希望走6步之后恰好形成“tencent”字符串，小红想知道，共有多少种不同的行走方案？

注意：每一步可以选择上，下，左，右任意一种方向行走，不可行走到矩阵外部。

## 输入描述
第一行输入两个正整数$n$, $m$，代表矩阵的行数和列数。

接下来的n行，每行输入一个长度为m，仅由小写字母组成的字符串，代表小红拿到的矩阵。

## 输出描述
一个整数，代表最终合法的方案数

## 示例1
输入：
```
3 3
ten
nec
ten
```

输出：
```
4
```

说明：

1. 从左上角出发，右右下左左上
2. 从左上角出发，右右下左左下
3. 从左下角出发，右右上左左下
4. 从左下角出发，右右上左左上

## 解题思路
dfs + 剪枝，符合加1，不符合直接返回

```python
def count_matrix(matrix: List[List[str]]):
    n, m = len(matrix), len(matrix[0])
    expect = ["t", "e", "n", "c", "e", "n", "t"]
    res = 0

    def dfs(i: int, j: int, count: int):
        if i < 0 or j < 0 or i >= n or j >= m:
            return
        
        if count >= len(expect) or matrix[i][j] != expect[count]:
            return
        
        if count == len(expect)-1:
            nonlocal res
            res += 1
        
        dfs(i+1, j, count+1)
        dfs(i-1, j, count+1)
        dfs(i, j+1, count+1)
        dfs(i, j-1, count+1)

    for i in range(n):
        for j in range(m):
            dfs(i, j, 0)

    return res
```