---
title: "leetcode-贪心129"
date: 2026-10-03T12:55:42+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 最长相邻不相等子序列 I

给定一个字符串数组 words ，和一个 二进制 数组 groups ，两个数组长度都是 n 。

如果 words 的一个 子序列 是交替的，那么对于序列中的任意两个连续字符串，它们在 groups 中相同索引的对应元素是 不同 的（也就是说，不能有连续的 0 或 1），

你需要从 words 中选出 最长交替子序列。

返回选出的子序列。如果有多个答案，返回 任意 一个。

注意：words 中的元素是不同的 。


```
impl Solution {
    pub fn get_longest_subsequence(words: Vec<String>, groups: Vec<i32>) -> Vec<String> {
        // 贪心：只要当前元素与上一个选中元素的分组不同，就选它。
        // 这样能保证交替且长度最长。
        words
            .into_iter()
            .zip(groups)
            .scan(None, |prev, (w, g)| {
                if *prev != Some(g) {
                    *prev = Some(g);
                    Some(Some(w))
                } else {
                    Some(None)
                }
            })
            .flatten()
            .collect()
    }
}
```
