---
title: "leetcode-双指针80"
date: 2026-09-05T09:53:05+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 数组中最大数对和的最小值

一个数对 (a,b) 的 数对和 等于 a + b 。最大数对和 是一个数对数组中最大的 数对和 。

比方说，如果我们有数对 (1,5) ，(2,3) 和 (4,4)，最大数对和 为 max(1+5, 2+3, 4+4) = max(6, 5, 8) = 8 。

给你一个长度为 偶数 n 的数组 nums ，请你将 nums 中的元素分成 n / 2 个数对，使得：

nums 中每个元素 恰好 在 一个 数对中，且
最大数对和 的值 最小 。

请你在最优数对划分的方案下，返回最小的 最大数对和 。


```
impl Solution {
    pub fn min_pair_sum(mut nums: Vec<i32>) -> i32 {
        nums.sort_unstable();

        // 最小化最大数对和：将最小与最大配对
        // 这样能使所有数对和尽可能均匀，降低最大值
        nums.iter()
            .zip(nums.iter().rev())
            .take(nums.len() / 2)
            .map(|(&a, &b)| a + b)
            .max()
            .unwrap_or(0)
    }
}
```
