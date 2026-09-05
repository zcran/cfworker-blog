---
title: "leetcode-前缀和115"
date: 2026-08-25T10:02:53+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 将数组分割为子数组的最小代价

给你两个长度相等的整数数组 nums 和 cost，和一个整数 k。

你可以将 nums 分割成多个子数组。第 i 个子数组由元素 nums[l..r] 组成，其代价为：

(nums[0] + nums[1] + ... + nums[r] + k * i) * (cost[l] + cost[l + 1] + ... + cost[r])。
注意，i 表示子数组的顺序：第一个子数组为 1，第二个为 2，依此类推。

返回通过任何有效划分得到的 最小 总代价。

子数组 是一个连续的 非空 元素序列。


```
impl Solution {
    pub fn minimum_cost(nums: Vec<i32>, cost: Vec<i32>, k: i32) -> i64 {
        let n = nums.len();
        let k = k as i64;

        // 前缀和：pref_cost[i] = cost[0..i) 的和
        let mut pref_cost = vec![0i64; n + 1];
        for i in 0..n {
            pref_cost[i + 1] = pref_cost[i] + cost[i] as i64;
        }
        let total_cost = pref_cost[n];

        // dp[i] = 前 i 个元素的最小总代价
        let mut dp = vec![i64::MAX; n + 1];
        dp[0] = 0;

        let mut sum_num = 0i64;
        for i in 1..=n {
            sum_num += nums[i - 1] as i64;

            // 枚举最后一个子数组的起始位置 j（0 <= j < i）
            for j in 0..i {
                // 最后一个子数组为 nums[j..i)
                // 代价 = sum_num * (pref_cost[i] - pref_cost[j]) + k * (total_cost - pref_cost[j])
                let subarray_sum = sum_num * (pref_cost[i] - pref_cost[j]);
                let extra = k * (total_cost - pref_cost[j]);
                dp[i] = dp[i].min(dp[j] + subarray_sum + extra);
            }
        }

        dp[n]
    }
}
```
