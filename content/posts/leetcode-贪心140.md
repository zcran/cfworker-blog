---
title: "leetcode-贪心140"
date: 2026-10-03T12:55:43+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 使数组中位数等于 K 的最少操作数

给你一个整数数组 nums 和一个 非负 整数 k 。一次操作中，你可以选择任一元素 加 1 或者减 1 。

请你返回将 nums 中位数 变为 k 所需要的 最少 操作次数。

一个数组的中位数指的是数组按非递减顺序排序后最中间的元素。如果数组长度为偶数，我们选择中间两个数的较大值为中位数。


```
impl Solution {
    pub fn min_operations_to_make_median_k(mut nums: Vec<i32>, k: i32) -> i64 {
        // 排序后，中位数下标 m = n / 2（偶数长度取偏右者）
        nums.sort_unstable();
        let m = nums.len() / 2;

        // 若中位数大于 k：需要把 [0..=m] 中所有大于 k 的元素降到 k，
        // 确保第 m 个元素最终为 k。
        // 若中位数小于 k：需要把 [m..] 中所有小于 k 的元素升到 k。
        // 中位数本身会被对应分支包含，且两个分支互斥，不会重复计算。
        if nums[m] > k {
            nums[..=m]
                .iter()
                .filter(|&&x| x > k)
                .map(|&x| (x - k) as i64)
                .sum()
        } else if nums[m] < k {
            nums[m..]
                .iter()
                .filter(|&&x| x < k)
                .map(|&x| (k - x) as i64)
                .sum()
        } else {
            0
        }
    }
}
```
