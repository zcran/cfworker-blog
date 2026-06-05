---
title: "leetcode-组合数学28"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 单调数组对的数目 I


给你一个长度为 n 的 正 整数数组 nums 。

如果两个 非负 整数数组 (arr1, arr2) 满足以下条件，我们称它们是 单调 数组对：

两个数组的长度都是 n 。
arr1 是单调 非递减 的，换句话说 arr1[0] <= arr1[1] <= ... <= arr1[n - 1] 。
arr2 是单调 非递增 的，换句话说 arr2[0] >= arr2[1] >= ... >= arr2[n - 1] 。
对于所有的 0 <= i <= n - 1 都有 arr1[i] + arr2[i] == nums[i] 。

请你返回所有 单调 数组对的数目。

由于答案可能很大，请你将它对 10^9 + 7 取余 后返回。


```
impl Solution {
    pub fn count_of_pairs(nums: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        const MAX_VAL: usize = 50; // 题目限定 nums[i] ≤ 50

        let n = nums.len();
        // dp[v] 表示当前索引 i 处，arr2[i] = v 的方案数
        let mut dp = vec![0i64; MAX_VAL + 1];

        // 初始化第一个位置：arr1[0] + arr2[0] = nums[0]，arr2[0] 可以取 0..=nums[0]
        for v in 0..=nums[0] as usize {
            dp[v] = 1;
        }

        for i in 1..n {
            let prev = nums[i - 1] as i64;
            let curr = nums[i] as i64;
            // 计算上一行 dp 的前缀和，prefix[t] = sum_{v=0}^{t-1} dp[v]
            let mut prefix = vec![0i64; MAX_VAL + 2];
            for v in 0..=MAX_VAL {
                prefix[v + 1] = (prefix[v] + dp[v]) % MOD;
            }

            let mut new_dp = vec![0i64; MAX_VAL + 1];
            for v2 in 0..=curr as usize {
                // 转移条件：v1 ≤ v2 且 v1 ≤ (prev - curr) + v2
                let bound = (v2 as i64).min(prev - curr + v2 as i64);
                if bound >= 0 {
                    let ub = bound as usize;
                    // 前缀和区间 [0, bound] 对应 prefix[ub+1]
                    new_dp[v2] = prefix[ub + 1];
                } // 否则为 0
            }
            dp = new_dp;
        }

        // 最终答案：最后一行的所有方案数之和
        dp.iter().fold(0, |acc, &x| (acc + x) % MOD) as i32
    }
}
```
