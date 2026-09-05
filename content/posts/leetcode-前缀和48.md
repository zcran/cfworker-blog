---
title: "leetcode-前缀和48"
date: 2026-08-25T10:02:49+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## K 次调整数组大小浪费的最小总空间

你正在设计一个动态数组。给你一个下标从 0 开始的整数数组 nums ，其中 nums[i] 是 i 时刻数组中的元素数目。除此以外，你还有一个整数 k ，表示你可以 调整 数组大小的 最多 次数（每次都可以调整成 任意 大小）。

t 时刻数组的大小 sizet 必须大于等于 nums[t] ，因为数组需要有足够的空间容纳所有元素。t 时刻 浪费的空间 为 sizet - nums[t] ，总 浪费空间为满足 0 <= t < nums.length 的每一个时刻 t 浪费的空间 之和 。

在调整数组大小不超过 k 次的前提下，请你返回 最小总浪费空间 。

注意：数组最开始时可以为 任意大小 ，且 不计入 调整大小的操作次数。


```
impl Solution {
    /// 计算在最多调整 k 次数组大小的情况下，最小总浪费空间
    ///
    /// 思路：动态规划
    /// dp[i][j] 表示处理到第 i 个时刻，还剩 j 次调整机会时的最小总空间
    /// 状态转移：枚举最后一次调整的位置，计算该区间内的最大元素 × 区间长度
    pub fn min_space_wasted_k_resizing(nums: Vec<i32>, k: i32) -> i32 {
        let n = nums.len();
        let k = k as usize;

        // 预处理：max_size[i][j] 表示区间 [i..=j] 的最大值
        let mut max_size = vec![vec![0; n]; n];
        for i in 0..n {
            let mut max_val = 0;
            for j in i..n {
                max_val = max_val.max(nums[j]);
                max_size[i][j] = max_val * (j - i + 1) as i32;
            }
        }

        // dp[i][j]：从第 i 个时刻开始，还剩 j 次调整机会的最小总空间
        // 使用 i64 避免溢出
        let mut dp = vec![vec![i64::MAX; k + 1]; n + 1];

        // 边界：没有剩余调整机会时，必须用当前区间的最大值作为大小
        for i in 0..n {
            dp[i][0] = max_size[i][n - 1] as i64;
        }
        dp[n][0] = 0;

        // 动态规划
        for j in 1..=k {
            for i in (0..n).rev() {
                // 如果没有剩余元素，无需空间
                if i == n {
                    dp[i][j] = 0;
                    continue;
                }

                // 枚举下一次调整的位置
                for mid in i..n {
                    let current = max_size[i][mid] as i64;
                    let rest = dp[mid + 1][j - 1];
                    if rest != i64::MAX {
                        dp[i][j] = dp[i][j].min(current + rest);
                    }
                }
            }
        }

        // 总浪费空间 = 总分配空间 - 实际使用的总空间
        let actual_used = nums.iter().sum::<i32>() as i64;
        (dp[0][k] - actual_used) as i32
    }
}
```
