---
title: "leetcode-数组15"
date: 2026-10-10T20:38:41+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 有序数组中出现次数超过25%的元素

给你一个非递减的 有序 整数数组，已知这个数组中恰好有一个整数，它的出现次数超过数组元素总数的 25%。

请你找到并返回这个整数


```
impl Solution {
    /// 在非递减有序数组中找出出现次数超过 25% 的元素。
    ///
    /// 关键观察：若某元素出现次数 > n/4，则它必然出现在
    /// 下标 `n/4`、`n/2`、`3n/4` 中的至少一个位置上。
    /// 只需检查这三个候选位置，并验证其出现次数是否超过 n/4。
    ///
    /// 时间复杂度 O(log n)（二分统计每个候选出现次数），空间复杂度 O(1)。
    pub fn find_special_integer(arr: Vec<i32>) -> i32 {
        let n = arr.len();
        let threshold = n / 4;

        for &i in &[n / 4, n / 2, 3 * n / 4] {
            let candidate = arr[i];
            let lo = arr.partition_point(|&x| x < candidate);
            let hi = arr.partition_point(|&x| x <= candidate);
            if hi - lo > threshold {
                return candidate;
            }
        }

        -1 // 题目保证存在，不会到达
    }
}
```
