---
title: "leetcode-数论50"
date: 2026-06-20T11:09:57+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 最大公约数相等的子序列数量

给你一个整数数组 nums。

请你统计所有满足以下条件的 非空 子序列 对 (seq1, seq2) 的数量：

子序列 seq1 和 seq2 不相交，意味着 nums 中 不存在 同时出现在两个序列中的下标。

seq1 元素的 GCD 等于 seq2 元素的 GCD。

返回满足条件的子序列对的总数。

由于答案可能非常大，请返回其对 10^9 + 7 取余 的结果。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn subsequence_pair_count(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let max_val = *nums.iter().max().unwrap() as usize;

        // 预计算 GCD 表，避免在 DP 过程中重复计算
        // g[a][b] = gcd(a, b)
        let mut gcd_table = vec![vec![0usize; max_val + 1]; max_val + 1];
        for i in 0..=max_val {
            for j in 0..=max_val {
                gcd_table[i][j] = Self::gcd(i as i32, j as i32) as usize;
            }
        }

        // 动态规划，状态展平为一维数组以提高缓存局部性
        // 状态：dp[x][y] 表示处理完当前元素后，两个子序列的 GCD 分别为 x 和 y 的方案数
        let size = max_val + 1;
        let mut dp_curr = vec![0i64; size * size];
        let mut dp_next = vec![0i64; size * size];

        // 初始状态：两个子序列都为空，GCD 均为 0
        dp_curr[0] = 1; // 索引 0 = (0, 0)

        // 遍历每个元素，决定将其放入 seq1、seq2 或都不放入
        for &num in &nums {
            let val = num as usize;
            dp_next.fill(0); // 清空下一层状态

            // 枚举所有可能的 GCD 状态
            for gcd1 in 0..=max_val {
                for gcd2 in 0..=max_val {
                    let count = dp_curr[gcd1 * size + gcd2];
                    if count == 0 {
                        continue; // 剪枝：跳过不可能的状态
                    }

                    // 选择 1：将当前元素放入 seq1
                    let new_gcd1 = gcd_table[gcd1][val];
                    let idx = new_gcd1 * size + gcd2;
                    dp_next[idx] = (dp_next[idx] + count) % MOD;

                    // 选择 2：将当前元素放入 seq2
                    let new_gcd2 = gcd_table[gcd2][val];
                    let idx = gcd1 * size + new_gcd2;
                    dp_next[idx] = (dp_next[idx] + count) % MOD;

                    // 选择 3：当前元素不放入任何子序列
                    let idx = gcd1 * size + gcd2;
                    dp_next[idx] = (dp_next[idx] + count) % MOD;
                }
            }

            // 交换当前层和下一层，继续迭代
            std::mem::swap(&mut dp_curr, &mut dp_next);
        }

        // 统计所有满足 gcd1 == gcd2 且非空的状态
        // 注意：排除两个子序列都为空的情况（x = 0）
        let mut result = 0i64;
        for gcd_val in 1..=max_val {
            let idx = gcd_val * size + gcd_val;
            result = (result + dp_curr[idx]) % MOD;
        }

        result as i32
    }

    /// 使用欧几里得算法计算两个数的最大公约数
    #[inline]
    fn gcd(mut a: i32, mut b: i32) -> i32 {
        while b != 0 {
            let temp = a % b;
            a = b;
            b = temp;
        }
        a
    }
}
```
