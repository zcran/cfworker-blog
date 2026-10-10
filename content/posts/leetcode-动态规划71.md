---
title: "leetcode-动态规划71"
date: 2026-10-05T20:39:58+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 最长定差子序列

给你一个整数数组 arr 和一个整数 difference，请你找出并返回 arr 中最长等差子序列的长度，该子序列中相邻元素之间的差等于 difference 。

子序列 是指在不改变其余元素顺序的情况下，通过删除一些元素或不删除任何元素而从 arr 派生出来的序列。


```
use std::collections::HashMap;

impl Solution {
    /// 1218. 最长定差子序列
    ///
    /// dp[x] 表示以数值 x 结尾、公差为 difference 的最长等差子序列长度。
    /// 遍历 arr 中的每个 x：
    ///   若存在前驱 x - difference，则 dp[x] = dp[x - difference] + 1；
    ///   否则以 x 单独作为一个子序列，dp[x] = 1。
    /// 用 HashMap 记录每个数值对应的最长长度，边遍历边更新，同时维护全局最大值。
    ///
    /// 一次遍历，时间 O(n)，空间 O(n)（存不同数值的 dp 值）。
    pub fn longest_subsequence(arr: Vec<i32>, difference: i32) -> i32 {
        let mut dp: HashMap<i32, i32> = HashMap::with_capacity(arr.len());
        let mut ans = 0;

        for x in arr {
            // 以 x 结尾的最长长度 = 以 x-difference 结尾的长度 + 1（无前驱则为 1）
            let len = dp.get(&(x - difference)).copied().unwrap_or(0) + 1;
            dp.insert(x, len); // x 可能重复出现，更新为更长值
            ans = ans.max(len);
        }

        ans
    }
}
```
