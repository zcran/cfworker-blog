---
title: "leetcode-二分查找87"
date: 2026-09-28T12:29:16+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 最短唯一子数组

给你一个整数数组 nums 。

找出 nums 中与其他任何 子数组 均 不 相同 的 子数组 的 最小 长度。

返回一个整数，表示此类 子数组 的 最小可能长度 。

子数组 是数组中的一个连续的非空元素序列。

如果两个 子数组 具有相同的长度，并且对应位置的元素也相同，则认为这两个 子数组 是相同的。


```
use std::collections::HashMap;

impl Solution {
    pub fn smallest_unique_subarray(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        const MOD: u64 = (1u64 << 61) - 1; // 大质数，减少碰撞
        const BASE: u64 = 131;

        // 预处理幂次和前缀哈希
        let mut pow = vec![1_u64; n + 1];
        let mut h = vec![0_u64; n + 1];
        for i in 1..=n {
            pow[i] = Self::mul_mod(pow[i - 1], BASE, MOD);
            h[i] = (Self::mul_mod(h[i - 1], BASE, MOD) + nums[i - 1] as u64) % MOD;
        }

        // 查询 [l, r]（1-based，闭区间）的哈希值
        let query = |l: usize, r: usize| -> u64 {
            (h[r] + MOD - Self::mul_mod(h[l - 1], pow[r - l + 1], MOD)) % MOD
        };

        // 判断是否存在长度为 len 的唯一子数组
        let has_unique = |len: usize| -> bool {
            let mut cnt: HashMap<u64, i32> = HashMap::with_capacity(n - len + 1);
            for l in 1..=n - len + 1 {
                *cnt.entry(query(l, l + len - 1)).or_insert(0) += 1;
            }
            cnt.values().any(|&c| c == 1)
        };

        // 二分最小长度
        let (mut left, mut right) = (1, n);
        while left < right {
            let mid = left + (right - left) / 2;
            if has_unique(mid) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        left as i32
    }

    // 用 u128 做乘法取模，避免溢出
    #[inline]
    fn mul_mod(a: u64, b: u64, m: u64) -> u64 {
        ((a as u128 * b as u128) % m as u128) as u64
    }
}
```
