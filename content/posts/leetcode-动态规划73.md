---
title: "leetcode-动态规划73"
date: 2026-10-05T20:39:58+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 掷骰子模拟

有一个骰子模拟器会每次投掷的时候生成一个 1 到 6 的随机数。

不过我们在使用它时有个约束，就是使得投掷骰子时，连续 掷出数字 i 的次数不能超过 rollMax[i]（i 从 1 开始编号）。

现在，给你一个整数数组 rollMax 和一个整数 n，请你来计算掷 n 次骰子可得到的不同点数序列的数量。

假如两个序列中至少存在一个元素不同，就认为这两个序列是不同的。由于答案可能很大，所以请返回 模 10^9 + 7 之后的结果。


```
impl Solution {
    /// 1223. 掷骰子模拟
    ///
    /// dp[j] 是一个滑动窗口（VecDeque），保存数字 j 结尾、连续出现 1..=roll_max[j] 次的序列数，
    /// 前端对应"连续 1 次"，末尾对应"连续 roll_max[j] 次"。
    /// sum[j] 维护 dp[j] 所有元素之和，避免每轮遍历窗口。
    ///
    /// 每掷一次骰子：
    ///   - 以 j 结尾的新序列，前一次必须不以 j 结尾，数量 = total - sum[j]；
    ///   - 把新值压入 dp[j] 前端，更新 sum[j]；
    ///   - 若窗口超过 roll_max[j]，弹出末尾最老的状态并同步扣减 sum[j]。
    ///
    /// 最终答案为所有 sum[j] 之和。整体 O(n)，空间 O(Σ roll_max)。
    pub fn die_simulator(n: i32, roll_max: Vec<i32>) -> i32 {
        use std::collections::VecDeque;
        const MOD: u64 = 1_000_000_007;

        let roll_max: Vec<usize> = roll_max.into_iter().map(|x| x as usize).collect();
        // dp[j]：数字 j 的滑动窗口，长度 ≤ roll_max[j]
        let mut dp: Vec<VecDeque<u64>> = vec![VecDeque::from([1u64]); 6];
        // sum[j]：数字 j 的所有状态计数之和（已取模）
        let mut sum = [1u64; 6];

        for _ in 1..n {
            // 本轮所有序列总数
            let total: u64 = sum.iter().sum::<u64>() % MOD;

            for j in 0..6 {
                // 新状态：前一位不是 j 的所有序列
                let new_val = (total + MOD - sum[j]) % MOD;
                dp[j].push_front(new_val);
                sum[j] = (sum[j] + new_val) % MOD;

                // 超出最大连续次数，弹出最老的状态
                if dp[j].len() > roll_max[j] {
                    if let Some(old) = dp[j].pop_back() {
                        sum[j] = (sum[j] + MOD - old) % MOD;
                    }
                }
            }
        }

        (sum.iter().sum::<u64>() % MOD) as i32
    }
}
```
