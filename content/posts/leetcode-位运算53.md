---
title: "leetcode-位运算53"
date: 2026-09-24T10:33:37+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 将数组分割成最多数目的子数组

给你一个只包含 非负 整数的数组 nums 。

我们定义满足 l <= r 的子数组 nums[l..r] 的分数为 nums[l] AND nums[l + 1] AND ... AND nums[r] ，其中 AND 是按位与运算。

请你将数组分割成一个或者更多子数组，满足：

每个 元素都 只 属于一个子数组。
子数组分数之和尽可能 小 。

请你在满足以上要求的条件下，返回 最多 可以得到多少个子数组。

一个 子数组 是一个数组中一段连续的元素。


```
impl Solution {
    pub fn max_subarrays(nums: Vec<i32>) -> i32 {
        // 要让子数组分数之和最小，就尽量让每段的 AND 为 0（0 是最小分数）。
        // 贪心地从左到右分段，一旦当前段 AND 变为 0 就切分并重新开始。
        // 若最终一段也没切成（整体 AND > 0），则只能分成 1 段。
        let mut count = 0;
        let mut cur = -1; // -1 全为 1，作为 AND 的单位元
        for x in nums {
            cur &= x;
            if cur == 0 {
                count += 1;
                cur = -1;
            }
        }
        count.max(1)
    }
}
```
