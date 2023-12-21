---
title: "Leetcode 计数排序2"
date: 2023-12-19T15:45:23+08:00
tags: ["leetcode", "计数排序"]
draft: false
---

## 数组拆分

给定长度为 2n 的整数数组 nums ，你的任务是将这些数分成 n 对, 例如 (a^1, b^1), (a^2, b^2), ..., (a^n, b^n) ，使得从 1 到 n 的 min(a^i, b^i) 总和最大。

返回该 最大总和 。

```
impl Solution {
    pub fn array_pair_sum(nums: Vec<i32>) -> i32 {
        let mut nums = nums;
        nums.sort_unstable();
        nums.iter().step_by(2).sum()
    }
}
```