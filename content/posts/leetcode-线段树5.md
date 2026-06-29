---
title: "leetcode-线段树5"
date: 2026-06-19T09:37:24+08:00
tags: ["leetcode", "线段树"]
draft: false
---


## 子数组不同元素数目的平方和 I

给你一个下标从 0 开始的整数数组 nums 。

定义 nums 一个子数组的 不同计数 值如下：

令 nums[i..j] 表示 nums 中所有下标在 i 到 j 范围内的元素构成的子数组（满足 0 <= i <= j < nums.length ），那么我们称子数组 nums[i..j] 中不同值的数目为 nums[i..j] 的不同计数。

请你返回 nums 中所有子数组的 不同计数 的 平方 和。

由于答案可能会很大，请你将它对 10^9 + 7 取余 后返回。

子数组指的是一个数组里面一段连续 非空 的元素序列。

```
impl Solution {
    /// 计算所有子数组的不同计数的平方和
    ///
    /// 思路：对于每个起点 i，向右扩展右端点 j，维护当前子数组的不同元素集合，
    /// 每次扩展后累加不同元素个数的平方。
    ///
    /// 时间复杂度 O(n^2)，空间复杂度 O(n)（最坏情况每个子数组都不同）
    /// 由于 n 最大 1000，此方法可行且简洁
    pub fn sum_counts(nums: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = nums.len();
        let mut total = 0i64;

        // 枚举每个起点
        for i in 0..n {
            // 使用 HashSet 记录当前子数组的不同元素
            let mut seen = std::collections::HashSet::new();

            // 从 i 开始，逐步扩展右端点
            for &num in &nums[i..] {
                seen.insert(num);
                let distinct_count = seen.len();
                total = (total + (distinct_count * distinct_count) as i64) % MOD;
            }
        }

        total as i32
    }
}
```
