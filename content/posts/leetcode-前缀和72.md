---
title: "leetcode-前缀和72"
date: 2026-08-25T10:02:50+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 使数组相等的最小开销

给你两个下标从 0 开始的数组 nums 和 cost ，分别包含 n 个 正 整数。

你可以执行下面操作 任意 次：

将 nums 中 任意 元素增加或者减小 1 。

对第 i 个元素执行一次操作的开销是 cost[i] 。

请你返回使 nums 中所有元素 相等 的 最少 总开销。


```
impl Solution {
    pub fn min_cost(nums: Vec<i32>, cost: Vec<i32>) -> i64 {
        // 按 nums 值排序，同时保持对应的 cost
        let mut pairs: Vec<_> = nums.iter().zip(cost.iter()).collect();
        pairs.sort_unstable_by_key(|&(num, _)| num);

        // 计算总开销，用于二分查找中判断前缀权重
        let total_cost: i64 = cost.iter().map(|&c| c as i64).sum();

        // 使用二分查找找到加权中位数
        // 加权中位数是使总开销最小的目标值
        let mut prefix_cost = 0i64;
        let mut target = 0;

        for &(&num, &c) in &pairs {
            prefix_cost += c as i64;
            if prefix_cost * 2 >= total_cost {
                target = num;
                break;
            }
        }

        // 计算将 nums 中所有元素变为 target 的总开销
        nums.iter()
            .zip(cost.iter())
            .map(|(&num, &c)| (num - target).abs() as i64 * c as i64)
            .sum()
    }
}
```
