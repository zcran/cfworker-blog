---
title: "leetcode-二分查找53"
date: 2026-09-28T12:29:13+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 找出数组排序后的目标下标

给你一个下标从 0 开始的整数数组 nums 以及一个目标元素 target 。

目标下标 是一个满足 nums[i] == target 的下标 i 。

将 nums 按 非递减 顺序排序后，返回由 nums 中目标下标组成的列表。如果不存在目标下标，返回一个 空 列表。返回的列表必须按 递增 顺序排列。


```
impl Solution {
    pub fn target_indices(mut nums: Vec<i32>, target: i32) -> Vec<i32> {
        // 排序后，target 会连续出现，其下标从 count(< target) 开始
        nums.sort_unstable();
        let start = nums.partition_point(|&x| x < target);
        let end = nums.partition_point(|&x| x <= target);

        // 不存在 target 时，start == end，返回空列表
        (start as i32..end as i32).collect()
    }
}
```
