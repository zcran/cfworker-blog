---
title: "leetcode-前缀和71"
date: 2026-08-25T10:02:50+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 最小化数组中的最大值

给你一个下标从 0 开始的数组 nums ，它含有 n 个非负整数。

每一步操作中，你需要：

选择一个满足 1 <= i < n 的整数 i ，且 nums[i] > 0 。
将 nums[i] 减 1 。
将 nums[i - 1] 加 1 。

你可以对数组执行 任意 次上述操作，请你返回可以得到的 nums 数组中 最大值 最小 为多少。


```
impl Solution {
    pub fn minimize_array_value(mut nums: Vec<i32>) -> i32 {
        // 使用 u64 避免溢出（n <= 1e5, nums[i] <= 1e9）
        let mut prefix_sum = 0u64;
        let mut max_avg = 0u64;

        for (i, &num) in nums.iter().enumerate() {
            let idx = i as u64;
            prefix_sum += num as u64;

            // 当前前缀的平均值向上取整
            // 因为每次操作只能向左传递，所以前缀和必须均匀分布在前 i+1 个元素中
            let avg = (prefix_sum + idx) / (idx + 1);
            max_avg = max_avg.max(avg);
        }

        max_avg as i32
    }
}
```
