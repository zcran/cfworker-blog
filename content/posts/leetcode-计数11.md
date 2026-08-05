---
title: "leetcode-计数11"
date: 2026-08-01T10:40:51+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 三数之和的多种可能

给定一个整数数组 arr ，以及一个整数 target 作为目标值，返回满足 i < j < k 且 arr[i] + arr[j] + arr[k] == target 的元组 i, j, k 的数量。

由于结果会非常大，请返回 10^9 + 7 的模。


```
impl Solution {
    pub fn three_sum_multi(arr: Vec<i32>, target: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        const MAX_VAL: usize = 100;

        // 统计每个数字出现的次数（数字范围 0-100）
        let mut count = [0; MAX_VAL + 1];
        for &num in &arr {
            count[num as usize] += 1;
        }

        let target = target as usize;
        let mut result = 0i64;

        // 枚举三元组 (i, j, k)，保证 i <= j <= k 避免重复组合
        for i in 0..=MAX_VAL {
            for j in i..=MAX_VAL {
                let k = target as i32 - i as i32 - j as i32;
                if k < 0 || k > MAX_VAL as i32 || k < j as i32 {
                    continue;
                }
                let k = k as usize;

                let ci = count[i];
                let cj = count[j];
                let ck = count[k];

                let ways = match (i == j, j == k) {
                    (true, true) => ci * (ci - 1) * (ci - 2) / 6,
                    (true, false) => ci * (ci - 1) * ck / 2,
                    (false, true) => ci * cj * (cj - 1) / 2,
                    (false, false) => ci * cj * ck,
                };

                result = (result + ways) % MOD;
            }
        }

        result as i32
    }
}
```
