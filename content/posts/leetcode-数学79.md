---
title: "leetcode-数学79"
date: 2026-10-07T20:39:33+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 最小操作次数使数组元素相等 III

给你一个整数数组 nums。

在一步操作中，你可以将任意单个元素 nums[i] 的值 增加 1。

返回使数组中的所有元素都 相等 所需的 最小总操作次数 。


```
impl Solution {
    pub fn min_moves(nums: Vec<i32>) -> i32 {
        // 只能增加元素，所以所有元素最终都等于当前最大值 max。
        // 最小操作次数 = 把每个元素补到 max 的总增量 = max * n - sum。
        let max = *nums.iter().max().unwrap() as i64;
        let sum: i64 = nums.iter().map(|&x| x as i64).sum();
        let n = nums.len() as i64;

        (max * n - sum) as i32
    }
}
```
