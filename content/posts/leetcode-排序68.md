---
title: "leetcode-排序68"
date: 2026-10-05T00:35:04+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 求出所有子序列的能量和

给你一个长度为 n 的整数数组 nums 和一个 正 整数 k 。

一个 子序列 的 能量 定义为子序列中 任意 两个元素的差值绝对值的 最小值 。

请你返回 nums 中长度 等于 k 的 所有 子序列的 能量和 。

由于答案可能会很大，将答案对 10^9 + 7 取余 后返回。


```
use std::collections::HashMap;

const MOD: i64 = 1_000_000_007;
const INF: i32 = i32::MAX;

impl Solution {
    pub fn sum_of_powers(mut nums: Vec<i32>, k: i32) -> i32 {
        nums.sort_unstable();
        let n = nums.len();
        let k = k as usize;

        // dp[i][p]：以 nums[i] 结尾、长度为 p 的所有子序列，
        // 按「当前最小差值」分组后的子序列个数。
        // 用 HashMap 按需存储，避免大量无用键占用内存。
        let mut dp: Vec<Vec<HashMap<i32, i64>>> = vec![vec![HashMap::new(); k + 1]; n];

        let mut result: i64 = 0;

        for i in 0..n {
            // 长度为 1 的子序列：最小差值约定为 INF（后续与真实差值取 min 时会被替换）
            dp[i][1].insert(INF, 1);

            for j in 0..i {
                let diff = nums[i] - nums[j];
                // 枚举由 nums[j] 结尾、长度为 p-1 的子序列，接上 nums[i] 形成长度 p
                for p in 2..=k {
                    // 先收集更新，避免同时借用 dp[j] 和 dp[i]
                    let updates: Vec<(i32, i64)> = dp[j][p - 1]
                        .iter()
                        .map(|(&v, &cnt)| (diff.min(v), cnt))
                        .collect();

                    let target = &mut dp[i][p];
                    for (key, cnt) in updates {
                        let entry = target.entry(key).or_insert(0);
                        *entry = (*entry + cnt) % MOD;
                    }
                }
            }

            // 累加以 nums[i] 结尾、长度为 k 的子序列的能量和
            for (&v, &cnt) in &dp[i][k] {
                result = (result + v as i64 * cnt) % MOD;
            }
        }

        result as i32
    }
}
```
