---
title: "leetcode-双指针110"
date: 2026-09-05T09:53:07+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 最小元素和最大元素的最小平均值

你有一个初始为空的浮点数数组 averages。另给你一个包含 n 个整数的数组 nums，其中 n 为偶数。

你需要重复以下步骤 n / 2 次：

从 nums 中移除 最小 的元素 minElement 和 最大 的元素 maxElement。

将 (minElement + maxElement) / 2 加入到 averages 中。

返回 averages 中的 最小 元素。


```
impl Solution {
    /// 每次取当前最小和最大的元素求平均，共取 n/2 次，返回这些平均值中的最小值。
    ///
    /// 思路：排序后，第 i 次取的就是 nums[i] 与 nums[n-1-i]。
    /// 遍历所有配对，维护平均值的最小值即可。
    /// 时间复杂度 O(n log n)，空间复杂度 O(1)。
    pub fn minimum_average(mut nums: Vec<i32>) -> f64 {
        nums.sort_unstable();
        let n = nums.len();
        let mut res = f64::INFINITY;

        for i in 0..n / 2 {
            let avg = (nums[i] as f64 + nums[n - 1 - i] as f64) / 2.0;
            if avg < res {
                res = avg;
            }
        }

        res
    }
}
```
