---
title: "leetcode-模拟103"
date: 2026-08-08T11:31:14+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 相同分数的最大操作数目 I

给你一个整数数组 nums ，如果 nums 至少 包含 2 个元素，你可以执行以下操作：

选择 nums 中的前两个元素并将它们删除。

一次操作的 分数 是被删除元素的和。

在确保 所有操作分数相同 的前提下，请你求出 最多 能进行多少次操作。

请你返回按照上述要求 最多 可以进行的操作次数。


```
impl Solution {
    pub fn max_operations(nums: Vec<i32>) -> i32 {
        let target = nums[0] + nums[1];
        let mut ops = 0;
        let mut i = 0;

        // 从头开始，每对元素之和必须等于 target
        while i + 1 < nums.len() && nums[i] + nums[i + 1] == target {
            ops += 1;
            i += 2;
        }

        ops
    }
}
```
