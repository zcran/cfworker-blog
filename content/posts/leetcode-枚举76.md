---
title: "leetcode-枚举76"
date: 2026-07-09T10:05:03+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 子数组操作后的最大频率

给你一个长度为 n 的数组 nums ，同时给你一个整数 k 。

你可以对 nums 执行以下操作 一次 ：

选择一个子数组 nums[i..j] ，其中 0 <= i <= j <= n - 1 。

选择一个整数 x 并将 nums[i..j] 中 所有 元素都增加 x 。

请你返回执行以上操作以后数组中 k 出现的 最大 频率。

子数组 是一个数组中一段连续 非空 的元素序列。




```
use std::collections::HashSet;

impl Solution {
    pub fn max_frequency(nums: Vec<i32>, k: i32) -> i32 {
        // 获取所有不同的目标值（包括 k 本身）
        let targets: HashSet<i32> = nums.iter().copied().collect();
        let mut ans = 0;

        // 对每个可能的目标值进行 DP 计算
        for &target in &targets {
            // dp0: 还没选择子数组时，k 出现的次数
            // dp1: 正在选择子数组（增加 target）时，k 出现的次数
            // dp2: 已经结束选择子数组后，k 出现的次数
            let mut dp0 = 0;
            let mut dp1 = 0;
            let mut dp2 = 0;

            for &x in &nums {
                // 更新 dp2：结束子数组（或继续在子数组后），k 出现的次数
                dp2 = dp2.max(dp1) + (x == k) as i32;
                // 更新 dp1：在子数组内，将 x 变为 target，k 出现的次数
                dp1 = dp1.max(dp0) + (x == target) as i32;
                // 更新 dp0：在子数组外，k 出现的次数
                dp0 += (x == k) as i32;
            }

            // 取三种状态的最大值
            ans = ans.max(dp1).max(dp2);
        }

        ans
    }
}
```
