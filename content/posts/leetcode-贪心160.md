---
title: "leetcode-贪心160"
date: 2026-10-03T12:55:44+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 最大子数组总值 I

给定一个长度为 n 的整数数组 nums 和一个整数 k。

你必须从 nums 中选择 恰好 k 个非空子数组 nums[l..r]。子数组可以重叠，同一个子数组（相同的 l 和 r）可以 被选择超过一次。

子数组 nums[l..r] 的 值 定义为：max(nums[l..r]) - min(nums[l..r])。

总值 是所有被选子数组的 值 之和。

返回你能实现的 最大 可能总值。

子数组 是数组中连续的 非空 元素序列。



```
impl Solution {
    pub fn max_total_value(nums: Vec<i32>, k: i32) -> i64 {
        // 每个子数组的值 = max - min，上限为整个数组的 (max - min)。
        // 由于同一个子数组（整个数组）可以被选 k 次，直接取最大值即可。
        let max = *nums.iter().max().unwrap() as i64;
        let min = *nums.iter().min().unwrap() as i64;
        (max - min) * k as i64
    }
}
```
