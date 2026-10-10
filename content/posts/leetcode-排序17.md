---
title: "leetcode-排序17"
date: 2026-10-05T00:35:00+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 子序列宽度之和

一个序列的 宽度 定义为该序列中最大元素和最小元素的差值。

给你一个整数数组 nums ，返回 nums 的所有非空 子序列 的 宽度之和 。由于答案可能非常大，请返回对 109 + 7 取余 后的结果。

子序列 定义为从一个数组里删除一些（或者不删除）元素，但不改变剩下元素的顺序得到的数组。例如，[3,6,2,7] 就是数组 [0,3,1,6,2,2,7] 的一个子序列。


```
const MOD: i64 = 1_000_000_007;

lazy_static! {
    /// 预计算 2^0 .. 2^100000 mod MOD，避免重复快速幂
    static ref POW2: Vec<i64> = {
        let mut pow = vec![1i64; 100_001];
        for i in 1..pow.len() {
            pow[i] = (pow[i - 1] << 1) % MOD;
        }
        pow
    };
}

impl Solution {
    /// 求所有非空子序列的宽度之和（对 1e9+7 取余）
    ///
    /// 思路：排序后，nums[i] 作为最大值的贡献为 nums[i] * 2^i，
    /// 作为最小值的贡献为 nums[i] * 2^(n-1-i)（符号相反）。
    /// 合并即每个元素净贡献 nums[i] * (2^i - 2^(n-1-i))。
    /// 时间 O(n log n)，额外空间 O(n)（预计算幂表）。
    pub fn sum_subseq_widths(mut nums: Vec<i32>) -> i32 {
        nums.sort_unstable();
        let n = nums.len();

        // 用 i64 累加，每步取模，避免溢出；+MOD 处理负系数
        let mut ans: i64 = 0;
        for (i, &num) in nums.iter().enumerate() {
            let coef = (POW2[i] - POW2[n - 1 - i] + MOD) % MOD;
            ans = (ans + coef * num as i64) % MOD;
        }

        ans as i32
    }
}
```
