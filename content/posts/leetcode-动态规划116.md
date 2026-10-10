---
title: "leetcode-动态规划116"
date: 2026-10-05T20:40:02+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 将数组划分成若干好子数组的方式

给你一个二元数组 nums 。

如果数组中的某个子数组 恰好 只存在 一 个值为 1 的元素，则认为该子数组是一个 好子数组 。

请你统计将数组 nums 划分成若干 好子数组 的方法数，并以整数形式返回。由于数字可能很大，返回其对 10^9 + 7 取余 之后的结果。

子数组是数组中的一个连续 非空 元素序列。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn number_of_good_subarray_splits(nums: Vec<i32>) -> i32 {
        // 每个好子数组恰好含一个 1，划分方案数等于
        // 相邻两个 1 之间可选的切分位置数之积（即它们下标的差值）
        let mut ans = 1i64;
        let mut prev = -1i32;

        for (i, &x) in nums.iter().enumerate() {
            if x == 0 {
                continue;
            }
            if prev >= 0 {
                ans = ans * (i as i64 - prev as i64) % MOD;
            }
            prev = i as i32;
        }

        // 没有 1 时无法划分，返回 0
        if prev < 0 { 0 } else { ans as i32 }
    }
}
```
