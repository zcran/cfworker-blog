---
title: "leetcode-排序47"
date: 2026-10-05T00:35:02+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 对奇偶下标分别排序

给你一个下标从 0 开始的整数数组 nums 。根据下述规则重排 nums 中的值：

1. 按 非递增 顺序排列 nums 奇数下标 上的所有值。
    · 举个例子，如果排序前 nums = [4,1,2,3] ，对奇数下标的值排序后变为 [4,3,2,1] 。奇数下标 1 和 3 的值按照非递增顺序重排。

2. 按 非递减 顺序排列 nums 偶数下标 上的所有值。
    · 举个例子，如果排序前 nums = [4,1,2,3] ，对偶数下标的值排序后变为 [2,1,4,3] 。偶数下标 0 和 2 的值按照非递减顺序重排。

返回重排 nums 的值之后形成的数组。


```
impl Solution {
    /// 按要求重排 nums：
    /// - 偶数下标上的值按非递减排序
    /// - 奇数下标上的值按非递增排序
    ///
    /// 思路：
    /// 分别取出偶数下标和奇数下标的值，各自排序后填回原位。
    pub fn sort_even_odd(nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();

        // 取出偶数下标的值，按非递减排序
        let mut even: Vec<i32> = nums.iter().step_by(2).copied().collect();
        even.sort_unstable();

        // 取出奇数下标的值，按非递增排序
        let mut odd: Vec<i32> = nums.iter().skip(1).step_by(2).copied().collect();
        odd.sort_unstable_by(|a, b| b.cmp(a));

        // 交错填回结果
        let mut res = Vec::with_capacity(n);
        for i in 0..n {
            if i % 2 == 0 {
                res.push(even[i / 2]);
            } else {
                res.push(odd[i / 2]);
            }
        }
        res
    }
}
```
