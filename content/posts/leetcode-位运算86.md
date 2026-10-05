---
title: "leetcode-位运算86"
date: 2026-09-24T10:33:39+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 有效 K 个不同元素子数组 I

给定一个整数数组 nums 和一个整数 k。

同时给定一个二维整数数组 queries，其中 queries[i] = [li, ri] 表示子数组 nums[li..ri]。

对于每个查询，如果子数组 nums[li..ri] 满足以下条件，则认为该 子数组 是 有效的：

它 恰好 包含 k 个 不同 的数字，并且
子数组中每个数字出现的 频率 都是 偶数。

返回一个布尔数组 ans，其中如果 nums[li..ri] 是 有效的，则 ans[i] 为 true，否则为 false。


```
use std::collections::HashMap;

impl Solution {
    pub fn valid_subarrays(nums: Vec<i32>, k: i32, queries: Vec<Vec<i32>>) -> Vec<bool> {
        // 思路：
        // 1. 用随机 64 位哈希给每个不同值映射一个随机数。子数组中每个数字出现偶数次
        //    ⇔ 该子数组的哈希异或和为 0（概率意义下）。
        //    前缀异或和 sum[i] = nums[0..i] 的哈希异或和，则 [l, r] 异或和为 0 ⇔ sum[r+1] == sum[l]。
        //
        // 2. 恰好包含 k 个不同数字：定义 lefts[i] = 使 nums[left..=i] 的不同数字个数 < 目标值 的最小 left。
        //    - l2[i] = 使窗口 [left..=i] 不同数字个数 < k  的 left（即最多 k-1 个）
        //    - l1[i] = 使窗口 [left..=i] 不同数字个数 < k+1 的 left（即最多 k 个）
        //    则 [l, r] 恰含 k 个不同数字 ⇔ l1[r] <= l < l2[r]。

        let n = nums.len();

        // 用 splitmix64 作为确定性伪随机数生成器（无需外部依赖，行为稳定）
        let mut state: u64 = 0x9E3779B97F4A7C15;
        let mut next_rand = || {
            state = state.wrapping_add(0x9E3779B97F4A7C15);
            let mut z = state;
            z = (z ^ (z >> 30)).wrapping_mul(0xBF58476D1CE4E5B9);
            z = (z ^ (z >> 27)).wrapping_mul(0x94D049BB133111EB);
            z ^ (z >> 31)
        };

        // 前缀哈希异或和
        let mut sum = vec![0u64; n + 1];
        let mut hash: HashMap<i32, u64> = HashMap::new();
        for i in 0..n {
            let h = *hash.entry(nums[i]).or_insert_with(|| next_rand() / 3);
            sum[i + 1] = sum[i] ^ h;
        }

        // calc_left(k)：lefts[i] = 使窗口 [left..=i] 中不同数字个数 < k 的最小 left
        let calc_left = |k: usize| -> Vec<usize> {
            let mut lefts = vec![0usize; n];
            let mut cnt: HashMap<i32, i32> = HashMap::new();
            let mut l = 0usize;
            for i in 0..n {
                *cnt.entry(nums[i]).or_insert(0) += 1;
                // 当不同数字个数达到 k 时，收缩左边界直到 < k
                while cnt.len() >= k {
                    let c = cnt.get_mut(&nums[l]).unwrap();
                    *c -= 1;
                    if *c == 0 {
                        cnt.remove(&nums[l]);
                    }
                    l += 1;
                }
                lefts[i] = l;
            }
            lefts
        };

        let l1 = calc_left(k as usize + 1); // 不同数字个数 < k+1，即 <= k
        let l2 = calc_left(k as usize);     // 不同数字个数 < k，即 <= k-1

        queries
            .iter()
            .map(|q| {
                let (l, r) = (q[0] as usize, q[1] as usize);
                // 条件：异或和为 0（频率全偶）且恰好 k 个不同数字
                sum[r + 1] == sum[l] && l1[r] <= l && l < l2[r]
            })
            .collect()
    }
}
```
