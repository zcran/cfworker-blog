---
title: "leetcode-滚动哈希3"
date: 2026-04-17T20:00:31+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---

## 最长重复子数组

给两个整数数组 nums1 和 nums2 ，返回 两个数组中 公共的 、长度最长的子数组的长度 。

```
use std::collections::HashSet;

impl Solution {
    /// 返回两个整数数组的最长公共子数组（连续）的长度。
    ///
    /// # 滚动哈希 + 二分搜索
    /// - 使用双哈希（两个大质数模数）避免碰撞。
    /// - 预计算基数幂和前缀哈希，O(1) 获取子数组哈希。
    /// - 二分答案，检查是否存在指定长度的公共子数组。
    ///
    /// # 参数
    /// - `nums1`: 第一个整数数组
    /// - `nums2`: 第二个整数数组
    ///
    /// # 返回值
    /// - 最长公共子数组的长度
    pub fn find_length(nums1: Vec<i32>, nums2: Vec<i32>) -> i32 {
        let n = nums1.len();
        let m = nums2.len();
        if n == 0 || m == 0 {
            return 0;
        }

        // 哈希参数（双模）
        const BASE: u64 = 91138233;               // 随机大基数
        const MOD1: u64 = 1_000_000_007;
        const MOD2: u64 = 1_000_000_009;

        // 将 i32 映射为 u64（防止负数干扰）
        let to_u64 = |x: i32| (x as i64 + 1_000_000_000) as u64;

        // 预计算幂（两个模数分别计算）
        let max_len = n.max(m);
        let mut pow1 = vec![1u64; max_len + 1];
        let mut pow2 = vec![1u64; max_len + 1];
        for i in 1..=max_len {
            pow1[i] = (pow1[i - 1] * BASE) % MOD1;
            pow2[i] = (pow2[i - 1] * BASE) % MOD2;
        }

        // 计算前缀哈希（两个数组，两个模数）
        let compute_prefix = |arr: &[i32]| -> (Vec<u64>, Vec<u64>) {
            let len = arr.len();
            let mut pref1 = vec![0u64; len + 1];
            let mut pref2 = vec![0u64; len + 1];
            for (i, &val) in arr.iter().enumerate() {
                let v = to_u64(val);
                pref1[i + 1] = (pref1[i] * BASE + v) % MOD1;
                pref2[i + 1] = (pref2[i] * BASE + v) % MOD2;
            }
            (pref1, pref2)
        };

        let (pref1_1, pref1_2) = compute_prefix(&nums1);
        let (pref2_1, pref2_2) = compute_prefix(&nums2);

        // 子数组哈希获取器（返回 (hash1, hash2)）
        let get_hash = |pref1: &[u64], pref2: &[u64], l: usize, r: usize| -> (u64, u64) {
            // 区间 [l, r) 的哈希：hash = pref[r] - pref[l] * BASE^(r-l)  mod MOD
            let len = r - l;
            let h1 = (pref1[r] + MOD1 - (pref1[l] * pow1[len]) % MOD1) % MOD1;
            let h2 = (pref2[r] + MOD2 - (pref2[l] * pow2[len]) % MOD2) % MOD2;
            (h1, h2)
        };

        // 二分查找最大长度
        let mut lo = 0;
        let mut hi = n.min(m);
        while lo < hi {
            let mid = (lo + hi + 1) / 2;
            if Self::check(mid, &nums1, &nums2, &get_hash, &pref1_1, &pref1_2, &pref2_1, &pref2_2) {
                lo = mid;
            } else {
                hi = mid - 1;
            }
        }
        lo as i32
    }

    /// 检查是否存在长度为 `len` 的公共子数组。
    ///
    /// # 参数
    /// - `len`: 待检查的长度
    /// - `nums1`, `nums2`: 原数组
    /// - `get_hash`: 快速获取子数组双哈希的闭包
    /// - `pref1_1`, `pref1_2`: nums1 的双模前缀哈希
    /// - `pref2_1`, `pref2_2`: nums2 的双模前缀哈希
    ///
    /// # 返回值
    /// - 存在公共子数组则返回 `true`，否则 `false`
    fn check(
        len: usize,
        nums1: &[i32],
        nums2: &[i32],
        get_hash: &dyn Fn(&[u64], &[u64], usize, usize) -> (u64, u64),
        pref1_1: &[u64],
        pref1_2: &[u64],
        pref2_1: &[u64],
        pref2_2: &[u64],
    ) -> bool {
        if len == 0 {
            return true;
        }
        let n = nums1.len();
        let m = nums2.len();

        // 收集 nums1 中所有长度为 len 的子数组的双哈希值
        let set1: HashSet<(u64, u64)> = (0..=n - len)
            .map(|i| get_hash(pref1_1, pref1_2, i, i + len))
            .collect();

        // 检查 nums2 中是否有子数组哈希值出现在 set1 中
        (0..=m - len).any(|i| set1.contains(&get_hash(pref2_1, pref2_2, i, i + len)))
    }
}
```
