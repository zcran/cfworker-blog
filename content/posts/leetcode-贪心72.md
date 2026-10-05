---
title: "leetcode-贪心72"
date: 2026-10-03T12:55:38+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 最少操作使数组递增

给你一个整数数组 nums （下标从 0 开始）。每一次操作中，你可以选择数组中一个元素，并将它增加 1 。

比方说，如果 nums = [1,2,3] ，你可以选择增加 nums[1] 得到 nums = [1,3,3] 。

请你返回使 nums 严格递增 的 最少 操作次数。

我们称数组 nums 是 严格递增的 ，当它满足对于所有的 0 <= i < nums.length - 1 都有 nums[i] < nums[i+1] 。一个长度为 1 的数组是严格递增的一种特殊情况。


```
impl Solution {
    pub fn min_operations(nums: Vec<i32>) -> i32 {
        // prev 是前一个元素调整后的值；当前元素至少要比 prev 大 1
        let mut prev = nums[0];
        let mut ops = 0;
        for &x in &nums[1..] {
            let target = (prev + 1).max(x);
            ops += target - x;
            prev = target;
        }
        ops
    }
}
```
