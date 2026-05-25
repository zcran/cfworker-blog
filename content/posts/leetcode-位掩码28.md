---
title: "leetcode-位掩码28"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 特别的排列

给你一个下标从 0 开始的整数数组 nums ，它包含 n 个 互不相同 的正整数。如果 nums 的一个排列满足以下条件，我们称它是一个特别的排列：

对于 0 <= i < n - 1 的下标 i ，要么 nums[i] % nums[i+1] == 0 ，要么 nums[i+1] % nums[i] == 0 。
请你返回特别排列的总数目，由于答案可能很大，请将它对 10⁹ + 7 取余 后返回。


```
impl Solution {
    pub fn special_perm(nums: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = nums.len();
        let full_mask = (1 << n) - 1;

        // 1. 整除关系矩阵
        let mut ok = vec![vec![false; n]; n];
        for i in 0..n {
            for j in 0..n {
                if i != j && (nums[i] % nums[j] == 0 || nums[j] % nums[i] == 0) {
                    ok[i][j] = true;
                }
            }
        }

        // 2. dp[mask][last] = 使用 mask 且最后一个数字为 last 的排列数
        let mut dp = vec![vec![0i64; n]; 1 << n];
        for i in 0..n {
            dp[1 << i][i] = 1; // 只有一个数字的排列
        }

        // 3. 枚举所有状态
        for mask in 1..=full_mask {
            for last in 0..n {
                if (mask >> last) & 1 == 0 { continue; }
                let cur = dp[mask][last];
                if cur == 0 { continue; }
                // 尝试添加下一个数字 next（不在 mask 中）
                for next in 0..n {
                    if (mask >> next) & 1 == 1 { continue; }
                    if !ok[last][next] { continue; }
                    let next_mask = mask | (1 << next);
                    dp[next_mask][next] = (dp[next_mask][next] + cur) % MOD;
                }
            }
        }

        // 4. 累加所有 full_mask 的末尾数字
        let mut ans = 0;
        for last in 0..n {
            ans = (ans + dp[full_mask][last]) % MOD;
        }
        ans as i32
    }
}
```
