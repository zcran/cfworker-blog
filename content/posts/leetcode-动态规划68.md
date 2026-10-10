---
title: "leetcode-动态规划68"
date: 2026-10-05T20:39:58+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 掷骰子等于目标和的方法数

这里有 n 个一样的骰子，每个骰子上都有 k 个面，分别标号为 1 到 k 。

给定三个整数 n、k 和 target，请返回投掷骰子的所有可能得到的结果（共有 kn 种方式），使得骰子面朝上的数字总和等于 target。

由于答案可能很大，你需要对 10^9 + 7 取模。


```
impl Solution {
    /// 1155. 掷骰子的 N 种方法
    ///
    /// 每个骰子取值 1..=k，n 个骰子和为 target 的方案数。
    /// 做变换：每个骰子取 x-1 ∈ 0..=k-1，则总和变为 target - n。
    /// 于是问题变为：n 个 [0, k-1] 的整数和为 S = target - n 的方案数。
    ///
    /// dp[j]：当前骰子数下和为 j 的方案数。每加一个骰子：
    ///   dp'[j] = Σ_{t=0}^{k-1} dp[j - t]
    /// 即 dp 与长度 k 的全 1 窗口卷积，可用"前缀和 + 滑动窗口差分"原地 O(1) 完成。
    ///
    /// 每轮做法：
    ///   1) 对 j 递增做前缀和：f[j] += f[j-1]（此时 f[j] = Σ_{t<=j} 旧f[t]）
    ///   2) 对 j 递减减去超出窗口的部分：f[j] -= f[j-k]
    /// 两趟均 O(max_j)，总体 O(n * (target - n))，空间 O(target - n)。
    pub fn num_rolls_to_target(n: i32, k: i32, target: i32) -> i32 {
        if target < n || target > n * k {
            return 0; // 无法组成 target
        }
        const MOD: i64 = 1_000_000_007;
        let (n, k, target) = (n as usize, k as usize, target as usize);
        let s = target - n; // 变换后的目标和

        let mut f = vec![0i64; s + 1];
        f[0] = 1;

        for i in 1..=n {
            // 当前 i 个骰子能达到的最大和
            let max_j = (i * (k - 1)).min(s);

            // 前缀和：f[j] = Σ_{t=0}^{j} 旧f[t]
            for j in 1..=max_j {
                f[j] = (f[j] + f[j - 1]) % MOD;
            }
            // 滑动窗口差分：减去超出 k 范围的部分
            for j in (k..=max_j).rev() {
                f[j] = (f[j] - f[j - k] + MOD) % MOD;
            }
        }

        f[s] as i32
    }
}
```
