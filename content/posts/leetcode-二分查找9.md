---
title: "leetcode-二分查找9"
date: 2026-09-28T12:29:10+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## H 指数 II

给你一个整数数组 citations ，其中 citations[i] 表示研究者的第 i 篇论文被引用的次数，citations 已经按照 非降序排列 。计算并返回该研究者的 h 指数。

h 指数的定义：h 代表“高引用次数”（high citations），一名科研人员的 h 指数是指他（她）的 （n 篇论文中）至少 有 h 篇论文分别被引用了至少 h 次。

请你设计并实现对数时间复杂度的算法解决此问题。


```
impl Solution {
    pub fn h_index(citations: Vec<i32>) -> i32 {
        // 性质：引用次数最高的 h 篇论文，其引用次数都 >= h
        // 该性质对 h 单调递减，故可二分。
        let n = citations.len();
        // 在 [left, right) 中二分，寻找最小的不满足性质的 h
        let (mut left, mut right) = (1, n + 1);
        while left < right {
            let mid = left + (right - left) / 2;
            // 最高的 mid 篇论文中，被引次数最少的一篇是 citations[n - mid]
            if citations[n - mid] >= mid as i32 {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        // left - 1 即最大的满足性质的 h
        left as i32 - 1
    }
}
```
