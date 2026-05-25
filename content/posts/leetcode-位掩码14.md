---
title: "leetcode-位掩码14"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 分配重复整数

给你一个长度为 n 的整数数组 nums ，这个数组中至多有 50 个不同的值。同时你有 m 个顾客的订单 quantity ，其中，整数 quantity[i] 是第 i 位顾客订单的数目。请你判断是否能将 nums 中的整数分配给这些顾客，且满足：

· 第 i 位顾客 恰好 有 quantity[i] 个整数。
· 第 i 位顾客拿到的整数都是 相同的 。
· 每位顾客都满足上述两个要求。

如果你可以分配 nums 中的整数满足上面的要求，那么请返回 true ，否则返回 false 。


```
use std::collections::HashMap;

impl Solution {
    pub fn can_distribute(nums: Vec<i32>, mut quantity: Vec<i32>) -> bool {
        // 统计频率并转为 Vec
        let mut freq = HashMap::new();
        for &x in &nums {
            *freq.entry(x).or_insert(0) += 1;
        }
        let mut counts: Vec<i32> = freq.into_values().collect();
        counts.sort_unstable_by(|a, b| b.cmp(a));
        quantity.sort_unstable_by(|a, b| b.cmp(a));

        // 纯函数递归：每次返回一个新的频率列表（克隆代价高，但符合函数式风格）
        fn dfs(counts: Vec<i32>, quantity: &[i32], idx: usize) -> bool {
            if idx == quantity.len() {
                return true;
            }
            let need = quantity[idx];
            counts.iter().enumerate().any(|(i, &c)| {
                if c >= need {
                    let mut new_counts = counts.clone(); // 不可变，生成新副本
                    new_counts[i] -= need;
                    dfs(new_counts, quantity, idx + 1)
                } else {
                    false
                }
            })
        }

        dfs(counts, &quantity, 0)
    }
}
```
