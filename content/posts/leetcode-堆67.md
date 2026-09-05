---
title: "leetcode-堆67"
date: 2026-08-21T09:54:14+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 能被 3 整除的三元组最大和

给你一个整数数组 nums。

你的任务是从 nums 中选择 恰好三个 整数，使得它们的和能被 3 整除。

返回这类三元组可能产生的 最大 和。如果不存在这样的三元组，返回 0。


```
impl Solution {
    /// 从 nums 中选择恰好三个整数，使它们的和能被 3 整除，返回最大和。
    /// 若不存在这样的三元组，返回 0。
    ///
    /// 思路：DP。
    /// f[j][r] 表示选 j 个数，余数为 r 时的最大和。
    /// 状态转移时倒序遍历 j，避免同一轮重复使用同一个数。
    ///
    /// 时间复杂度：O(n * 3 * 3) = O(n)
    /// 空间复杂度：O(3 * 3) = O(1)
    pub fn maximum_sum(nums: Vec<i32>) -> i32 {
        const K: usize = 3;      // 恰好选 3 个数
        const MOD: usize = 3;    // 模数
        const NEG_INF: i32 = i32::MIN / 2; // 足够小的负数，避免加法溢出

        // f[j][r]：选 j 个数，和模 3 余 r 的最大和
        let mut f = [[NEG_INF; MOD]; K + 1];
        f[0][0] = 0;

        for x in nums {
            let rem = (x % MOD as i32 + MOD as i32) % MOD as i32; // 保证非负
            // 倒序遍历 j，确保每个数只用一次
            for j in (1..=K).rev() {
                for r in 0..MOD {
                    let prev_r = (r + MOD - rem as usize) % MOD;
                    let candidate = f[j - 1][prev_r].saturating_add(x);
                    if candidate > f[j][r] {
                        f[j][r] = candidate;
                    }
                }
            }
        }

        f[K][0].max(0)
    }
}
```
