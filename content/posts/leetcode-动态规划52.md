---
title: "leetcode-动态规划52"
date: 2026-10-05T20:39:57+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 盈利计划

集团里有 n 名员工，他们可以完成各种各样的工作创造利润。

第 i 种工作会产生 profit[i] 的利润，它要求 group[i] 名成员共同参与。如果成员参与了其中一项工作，就不能参与另一项工作。

工作的任何至少产生 minProfit 利润的子集称为 盈利计划 。并且工作的成员总数最多为 n 。

有多少种计划可以选择？因为答案很大，所以 返回结果模 10^9 + 7 的值。


```
impl Solution {
    /// 879. 盈利计划
    ///
    /// dp[j][k]：使用恰好 j 名员工、获得利润至少为 k（超过 minProfit 的部分统一封顶）的方案数。
    /// 初始 dp[0][0] = 1。对每项工作 (g, p)，从大到小倒序遍历 j 和 k 做 0/1 背包更新：
    ///   dp[j][k] += dp[j - g][max(0, k - p)]
    /// 最终答案为 Σ_j dp[j][minProfit]。
    ///
    /// 用一维滚动数组（j 从 n 递减、k 从 minProfit 递减）避免使用 HashMap，空间 O(n * minProfit)。
    pub fn profitable_schemes(n: i32, min_profit: i32, group: Vec<i32>, profit: Vec<i32>) -> i32 {
        const MOD: i32 = 1_000_000_007;
        let n = n as usize;
        let p = min_profit as usize;

        // dp[j][k]：用 j 名员工、利润封顶到 k 的方案数
        let mut dp = vec![vec![0i32; p + 1]; n + 1];
        dp[0][0] = 1;

        for (g, pr) in group.iter().zip(profit.iter()) {
            let (g, pr) = (*g as usize, *pr as usize);
            // 倒序保证每项工作只用一次
            for j in (g..=n).rev() {
                for k in (0..=p).rev() {
                    // 取这项工作后，剩余利润需求为 max(0, k - pr)
                    let prev = if k >= pr { k - pr } else { 0 };
                    dp[j][k] = (dp[j][k] + dp[j - g][prev]) % MOD;
                }
            }
        }

        // 利润至少为 minProfit 的方案：k 已封顶到 p，直接累加 j 从 0..=n
        dp.iter().map(|row| row[p]).fold(0, |acc, v| (acc + v) % MOD)
    }
}
```
