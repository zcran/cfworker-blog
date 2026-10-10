---
title: "leetcode-排序26"
date: 2026-10-05T00:35:01+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 数组序号转换

给你一个整数数组 arr ，请你将数组中的每个元素替换为它们排序后的序号。

序号代表了一个元素有多大。序号编号的规则如下：

序号从 1 开始编号。
一个元素越大，那么序号越大。如果两个元素相等，那么它们的序号相同。
每个数字的序号都应该尽可能地小。


```
impl Solution {
    /// 将每个元素替换为其排序后的序号（从 1 开始，相同值同序号）
    ///
    /// 思路：把 (值, 原下标) 一起排序，再顺序扫描分配序号。
    /// 相邻值相同则序号不变，否则序号 +1，最后按下标回填。
    /// 时间 O(n log n)，额外空间 O(n)。
    pub fn array_rank_transform(arr: Vec<i32>) -> Vec<i32> {
        let n = arr.len();

        // (值, 原下标) 排序，保证相同值聚在一起且下标不丢失
        let mut rank: Vec<(i32, usize)> = arr.iter().copied().enumerate().map(|(i, v)| (v, i)).collect();
        rank.sort_unstable();

        let mut ans = vec![0i32; n];
        let mut cur = 0; // 当前序号

        for (i, &(v, idx)) in rank.iter().enumerate() {
            // 首个元素，或值比前一个大，序号 +1
            if i == 0 || v != rank[i - 1].0 {
                cur += 1;
            }
            ans[idx] = cur;
        }

        ans
    }
}
```
