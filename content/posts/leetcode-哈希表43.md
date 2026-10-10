---
title: "leetcode-哈希表43"
date: 2026-10-09T20:07:58+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 大餐计数

大餐 是指 恰好包含两道不同餐品 的一餐，其美味程度之和等于 2 的幂。

你可以搭配 任意 两道餐品做一顿大餐。

给你一个整数数组 deliciousness ，其中 deliciousness[i] 是第 i 道餐品的美味程度，返回你可以用数组中的餐品做出的不同 大餐 的数量。结果需要对 10^9 + 7 取余。

注意，只要餐品下标不同，就可以认为是不同的餐品，即便它们的美味程度相同。


```
use std::collections::HashMap;

impl Solution {
    pub fn count_pairs(deliciousness: Vec<i32>) -> i32 {
        // 遍历数组，对每个新元素 x，统计已出现过且 x + y 为 2 的幂的 y 的数量。
        // 美味程度 <= 2^20，两数之和 <= 2^21，枚举 2^0..2^21 即可。
        const MOD: i64 = 1_000_000_007;
        let mut cnt: HashMap<i32, i64> = HashMap::new();
        let mut ans: i64 = 0;

        for &x in &deliciousness {
            // 枚举可能的 2 的幂，查找互补值 y = p - x。
            let mut p = 1i64;
            while p <= (1i64 << 21) {
                if let Some(&c) = cnt.get(&(p as i32 - x)) {
                    ans = (ans + c) % MOD;
                }
                p <<= 1;
            }
            *cnt.entry(x).or_insert(0) += 1;
        }

        ans as i32
    }
}
```
