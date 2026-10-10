---
title: "leetcode-动态规划175"
date: 2026-10-05T20:40:06+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 可整除游戏

给你一个长度为 n 的整数数组 nums。

Alice 和 Bob 正在玩一个游戏。Alice 会选择：

· 一个整数 k，满足 k > 1。
· 两个整数 l 和 r，满足 0 <= l <= r < n。

初始时，Alice 和 Bob 的分数都为 0。

对于区间 [l, r]（包含两端）中的每个下标 i：

· 如果 nums[i] 能被 k 整除，则 Alice 的分数 增加 nums[i]。
· 否则，Bob 的分数 增加 nums[i]。

分数差 定义为 Alice 的分数 减去 Bob 的分数。

Alice 希望 最大化 分数差。如果有多个 k 可以达到 最大 分数差，她会选择其中 最小 的 k。

返回 最大 分数差与所选 k 的 乘积 。由于结果可能很大，请返回其对 109 + 7 取余数后的结果。




```
impl Solution {
    pub fn divisible_game(nums: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = nums.len();

        // 前缀和，用于快速计算区间内非倍数元素之和
        let mut prefix = vec![0i64; n + 1];
        for i in 0..n {
            prefix[i + 1] = prefix[i] + nums[i] as i64;
        }

        // 收集每个质数对应的下标列表
        let mut prime_to_indices: std::collections::BTreeMap<i32, Vec<usize>> =
            std::collections::BTreeMap::new();

        for (i, &x) in nums.iter().enumerate() {
            let mut v = x;
            let mut p = 2;
            while p * p <= v {
                if v % p == 0 {
                    prime_to_indices.entry(p).or_default().push(i);
                    while v % p == 0 {
                        v /= p;
                    }
                }
                p += 1;
            }
            if v > 1 {
                prime_to_indices.entry(v).or_default().push(i);
            }
        }

        // 所有数都是 1 时，只能选一个 1，分数差 -1，最小 k = 2
        if prime_to_indices.is_empty() {
            return (MOD - 2) as i32;
        }

        let mut best_diff = i64::MIN;
        let mut best_k = 0i32;

        // 对每个质数 k，将被 k 整除的元素视为正收益，否则为负
        // 最大子数组和 = 遍历下标列表，累加 "正元素 + 之间负元素之和"
        for (&k, positions) in &prime_to_indices {
            let mut cur = 0i64;
            let mut max_sum = i64::MIN;
            let mut last_end = 0; // 上一段结束后的下一个位置

            for &pos in positions {
                // 从 last_end 到 pos 之间（不含 pos）的元素都不能被 k 整除，作为负贡献
                let gap = prefix[pos] - prefix[last_end];
                cur = (cur - gap).max(0) + nums[pos] as i64;
                max_sum = max_sum.max(cur);
                last_end = pos + 1;
            }

            // 取分数差更大者；相同则取更小的 k
            if max_sum > best_diff || (max_sum == best_diff && k < best_k) {
                best_diff = max_sum;
                best_k = k;
            }
        }

        let ans = best_diff % MOD * best_k as i64 % MOD;
        if ans < 0 { (ans + MOD) as i32 } else { ans as i32 }
    }
}
```
