---
title: "leetcode-动态规划110"
date: 2026-10-05T20:40:01+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 好分区的数目

给你一个正整数数组 nums 和一个整数 k 。

分区 的定义是：将数组划分成两个有序的 组 ，并满足每个元素 恰好 存在于 某一个 组中。如果分区中每个组的元素和都大于等于 k ，则认为分区是一个好分区。

返回 不同 的好分区的数目。由于答案可能很大，请返回对 10^9 + 7 取余 后的结果。

如果在两个分区中，存在某个元素 nums[i] 被分在不同的组中，则认为这两个分区不同。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn count_partitions(nums: Vec<i32>, k: i32) -> i32 {
        let k = k as i64;
        let total: i64 = nums.iter().map(|&x| x as i64).sum();

        // 总和不足以让两组都 ≥ k，必然无好分区
        if total < 2 * k {
            return 0;
        }

        // 用容斥：全部 2^n 种分区，减去「某一组和 < k」的分区
        // 由于两组和 < k 的情况不可能同时发生（总和 ≥ 2k），无需考虑重复
        let mut ans = 1i64;

        // f[j]: 选取部分元素使其和恰为 j 的方案数（j < k）
        let mut f = vec![0i64; k as usize];
        f[0] = 1;

        for &x in &nums {
            let x = x as i64;
            ans = ans * 2 % MOD;
            // 0-1 背包，从大到小更新，仅统计和小于 k 的方案
            for j in (x..k).rev() {
                f[j as usize] = (f[j as usize] + f[(j - x) as usize]) % MOD;
            }
        }

        // 减去两组各自「和 < k」的方案数（每种贡献 2 倍，分别对应第一组/第二组）
        for &v in &f {
            ans = (ans - v * 2 % MOD + MOD) % MOD;
        }

        ans as i32
    }
}
```
