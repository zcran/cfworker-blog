---
title: "leetcode-动态规划34"
date: 2026-10-05T20:39:56+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## K 个逆序对数组

对于一个整数数组 nums，逆序对是一对满足 0 <= i < j < nums.length 且 nums[i] > nums[j]的整数对 [i, j] 。

给你两个整数 n 和 k，找出所有包含从 1 到 n 的数字，且恰好拥有 k 个 逆序对 的不同的数组的个数。由于答案可能很大，只需要返回对 10^9 + 7 取余的结果。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn k_inverse_pairs(n: i32, k: i32) -> i32 {
        let (n, k) = (n as usize, k as usize);

        // dp[j]：当前长度下恰好有 j 个逆序对的排列数
        // 借助前缀和思想：dp_new[j] = sum(dp_old[j - t]) for t in 0..=min(j, i-1)
        let mut dp = vec![0i64; k + 1];
        dp[0] = 1;

        for i in 1..=n {
            let mut next = vec![0i64; k + 1];
            let mut window = 0i64; // 滑动窗口内 dp 的和
            for j in 0..=k {
                // 加入 dp[j]，当窗口超过 i 个元素时减去最左边的
                window += dp[j];
                if j >= i {
                    window -= dp[j - i];
                }
                next[j] = window % MOD;
            }
            dp = next;
        }

        dp[k] as i32
    }
}
```
