---
title: "leetcode-前缀和86"
date: 2026-08-25T10:02:51+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 英雄的力量

给你一个下标从 0 开始的整数数组 nums ，它表示英雄的能力值。如果我们选出一部分英雄，这组英雄的 力量 定义为：

i0 ，i1 ，... ik 表示这组英雄在数组中的下标。那么这组英雄的力量为 max(nums[i0],nums[i1] ... nums[ik])2 * min(nums[i0],nums[i1] ... nums[ik]) 。

请你返回所有可能的 非空 英雄组的 力量 之和。由于答案可能非常大，请你将结果对 10^9 + 7 取余。


```
impl Solution {
    pub fn sum_of_power(mut nums: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;

        // 排序，使每个元素作为最大值时，最小值来自左侧
        nums.sort_unstable();
        let n = nums.len();

        // dp[i] 表示以 nums[i] 为最大值的所有子序列的 min 之和
        // 实际上，dp[i] = nums[i] + sum(dp[j]) for j < i
        let mut dp_sum = 0i64;      // 所有 dp 值的和，即 pre_dpsum
        let mut total_sum = 0i64;   // 当前 dp 值累计和

        let mut ans = 0i64;

        for (i, &num) in nums.iter().enumerate() {
            let num = num as i64;

            // dp[i] = nums[i] + 所有以更小元素为最大值的子序列的 min 之和
            // 因为每个更小元素都可以和 nums[i] 组合
            let dp = (num + total_sum) % MOD;

            // 更新累计和
            total_sum = (total_sum + dp) % MOD;

            // 以 nums[i] 为最大值的所有子序列贡献：
            // sum = nums[i]^2 * min_sum，其中 min_sum = dp
            ans = (ans + (num * num % MOD) * dp) % MOD;
        }

        ans as i32
    }
}
```
