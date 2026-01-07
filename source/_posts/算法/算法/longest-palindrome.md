---
title: 最长回文子串问题的解法
tags: [Algorithm]
categories: Algorithm
cover: images/Algorithm/Algorithm.png
excerpt: 本文记录最长回文子串问题的几种解决方法
---
<!-- toc -->

## 问题分析
力扣链接：[https://leetcode.cn/problems/longest-palindromic-substring/description](https://leetcode.cn/problems/longest-palindromic-substring/description)

给你一个字符串`s`，找到`s`种最长的回文子串
例如 `s = "babbad"` 的最长回文子串是 `abba`，长度为4

## 判断方法
从两边向中间，或者中间向两边拓展，不断比较头尾字符是否相同即可

## 中心拓展方法
思路：遍历每一个字符，向两百年拓展找到一起为中心的最长回文子串，所有找到的回文子串的最大长度即所求

```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        longest = ""
        for i in range(len(s)):
            # odd
            p = self.getPalindrome(s, i, i)
            if len(p) > len(longest):
                longest = p
            
            # even
            p = self.getPalindrome(s, i, i+1)
            if len(p) > len(longest):
                longest = p
            
        return longest
    
    def getPalindrome(self, s: str, left: int, right: int) -> str:
        while left >= 0 and right < len(s) and s[left] == s[right]:
            left -= 1
            right += 1
        
        return s[left+1:right]
```

## 二维动态规划方法
回文串两边加上两个相同字符，会变成一个新的回文串，建立二维数组dp，找出所有的回文子串，`dp[i][j]`记录子串i到j是否为回文串

1. 初始化dp数组，所有一个字母都是回文串，所以`dp[i][i] = True`
2. 得出递推关系：
    - 如果`s[i]`和`s[j]`相等，并且子串`dp[i+1][j-1] = True`，那么`dp[i][j]`也为true
    - 不相等，直接False

```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        n = len(s)
        memo = [[None] * n for _ in range(n)]
        max_length = 1
        longest_start = 0

        def dp(i, j):
            nonlocal max_length, longest_start, memo

            if i >= j:
                return True
            if memo[i][j] is not None:
                return memo[i][j]
            
            if s[i] == s[j] and dp(i+1, j-1):
                memo[i][j] = True
                if j-i+1 > max_length:
                    max_length = j-i+1
                    longest_start = i
            else:
                memo[i][j] = False
            return memo[i][j]

        for i in range(n):
            for j in range(i, n):
                dp(i, j)
        
        return s[longest_start:longest_start+max_length]
```