---
title: "leetcode-贪心169"
date: 2026-10-03T12:55:45+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 变成目标数组的最少操作次数

给你两个长度为 n 的整数数组 nums 和 target，其中 nums[i] 是下标 i 处的当前值，而 target[i] 是下标 i 处的期望值。

你可以执行以下操作任意次数（包括零次）：

· 选择一个整数值 x
· 找到所有 极大连续段，使得 nums[i] == x（如果一个段在保持所有值等于 x 的情况下无法向左或向右延伸，则该段是 极大 的）
· 对于每个这样的段 [l, r]，同时 进行更新：
    · nums[l] = target[l], nums[l + 1] = target[l + 1], ..., nums[r] = target[r]

返回使 nums 等于 target 所需的 最小 操作次数。


```
use std::collections::HashSet;

impl Solution {
    pub fn min_operations(nums: Vec<i32>, target: Vec<i32>) -> i32 {
        // 观察：一次操作针对某个值 x，能一次性把 nums 中所有值为 x 的
        // 极大连续段统一改成对应的 target 值。
        // 因此答案就是「所有满足 nums[i] != target[i] 的位置上，
        // nums[i] 的不同取值个数」——每个不同的值需要一次操作。
        nums.iter()
            .zip(target.iter())
            .filter(|&(&a, &b)| a != b)
            .map(|(&a, _)| a)
            .collect::<HashSet<_>>()
            .len() as i32
    }
}
```
