---
title: "leetcode-模拟74"
date: 2026-08-08T11:31:13+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 使数组中所有元素都等于零

给你一个非负整数数组 nums 。在一步操作中，你必须：

选出一个正整数 x ，x 需要小于或等于 nums 中 最小 的 非零 元素。

nums 中的每个正整数都减去 x。

返回使 nums 中所有元素都等于 0 需要的 最少 操作数。


```
impl Solution {
    /// 每次操作将所有正整数减去当前最小正整数，
    /// 等价于统计数组中不同正整数的个数。
    ///
    /// 例如 [1,5,0,3,5] 中不同正数为 {1,3,5}，共 3 次操作。
    pub fn minimum_operations(nums: Vec<i32>) -> i32 {
        let mut seen = [false; 101]; // 约束: nums[i] <= 100
        let mut distinct = 0;

        for &x in &nums {
            if x != 0 && !seen[x as usize] {
                seen[x as usize] = true;
                distinct += 1;
            }
        }

        distinct
    }
}
```
