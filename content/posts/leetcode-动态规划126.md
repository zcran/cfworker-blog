---
title: "leetcode-动态规划126"
date: 2026-10-05T20:40:02+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 使数组变美的最小增量运算数

给你一个下标从 0 开始、长度为 n 的整数数组 nums ，和一个整数 k 。

你可以执行下述 递增 运算 任意 次（可以是 0 次）：

从范围 [0, n - 1] 中选择一个下标 i ，并将 nums[i] 的值加 1 。

如果数组中任何长度 大于或等于 3 的子数组，其 最大 元素都大于或等于 k ，则认为数组是一个 美丽数组 。

以整数形式返回使数组变为 美丽数组 需要执行的 最小 递增运算数。

子数组是数组中的一个连续 非空 元素序列。


```
impl Solution {
    pub fn min_increment_operations(nums: Vec<i32>, k: i32) -> i64 {
        let n = nums.len();
        // dp[i][j]: 处理到下标 i 时，当前位置左侧已连续有 j 个元素「未达标」的最小操作数
        // j ∈ {0, 1, 2}，当 j 达到 3 时非法（因为长度 ≥ 3 的子数组必须含一个达标元素）
        // 用滚动数组优化空间
        const INF: i64 = i64::MAX / 2;
        let mut dp = [0i64; 3];

        for &x in &nums {
            let need = (k - x).max(0) as i64; // 把当前元素提升到 k 所需操作数
            let mut ndp = [INF; 3];

            for j in 0..3 {
                if dp[j] == INF {
                    continue;
                }
                // 选择一：当前元素增大到 ≥ k，左侧连续未达标长度清零
                ndp[0] = ndp[0].min(dp[j] + need);
                // 选择二：当前元素不增大，左侧连续未达标长度 +1（必须 < 3）
                if j < 2 {
                    ndp[j + 1] = ndp[j + 1].min(dp[j]);
                }
            }
            dp = ndp;
        }

        // 取最终三种状态中的最小值
        dp[0].min(dp[1]).min(dp[2])
    }
}
```
