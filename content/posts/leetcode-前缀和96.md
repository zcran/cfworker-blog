---
title: "leetcode-前缀和96"
date: 2026-08-25T10:02:52+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## K 个不相交子数组的最大能量值

给你一个长度为 n 下标从 0 开始的整数数组 nums 和一个 正奇数 整数 k 。

x 个子数组的能量值定义为 strength = sum[1] * x - sum[2] * (x - 1) + sum[3] * (x - 2) - sum[4] * (x - 3) + ... + sum[x] * 1 ，其中 sum[i] 是第 i 个子数组的和。更正式的，能量值是满足 1 <= i <= x 的所有 i 对应的 (-1)^(i+1) * sum[i] * (x - i + 1) 之和。

你需要在 nums 中选择 k 个 不相交子数组 ，使得 能量值最大 。

请你返回可以得到的 最大能量值 。

注意，选出来的所有子数组 不 需要覆盖整个数组。


```
impl Solution {
    pub fn maximum_strength(nums: Vec<i32>, k: i32) -> i64 {
        let k = k as usize;
        let n = nums.len();
        let nums: Vec<i64> = nums.iter().map(|&x| x as i64).collect();

        // dp[i][j]: 从前j个元素中选i个不相交子数组的最大能量值
        let mut dp = vec![vec![i64::MIN / 2; n + 1]; k + 1];
        dp[0].fill(0); // 0个子数组能量为0

        for i in 1..=k {
            let weight = if i % 2 == 1 { (k - i + 1) as i64 } else { -((k - i + 1) as i64) };
            let mut best = i64::MIN / 2;

            // j至少为i（每个子数组至少1个元素），且需要为剩余子数组预留空间
            for j in i..=n - (k - i) {
                // 当前元素可以作为新子数组的结尾，或延续前一个子数组
                best = best.max(dp[i - 1][j - 1]) + nums[j - 1] * weight;
                // 不选当前元素，或选但使用更优的前缀
                dp[i][j] = dp[i][j - 1].max(best);
            }
        }

        dp[k][n]
    }
}
```
