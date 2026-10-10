---
title: "leetcode-排序23"
date: 2026-10-05T00:35:00+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 查询无效交易

如果出现下述两种情况，交易 可能无效：

交易金额超过 $1000

或者，它和 另一个城市 中 同名 的另一笔交易相隔不超过 60 分钟（包含 60 分钟整）

给定字符串数组交易清单 transaction 。每个交易字符串 transactions[i] 由一些用逗号分隔的值组成，这些值分别表示交易的名称，时间（以分钟计），金额以及城市。

返回 transactions，返回可能无效的交易列表。你可以按 任何顺序 返回答案。


```
use std::collections::{HashMap, HashSet};

impl Solution {
    /// 返回所有可能无效的交易
    ///
    /// 无效条件：
    ///   1. 金额 > 1000；
    ///   2. 与「另一城市中同名」的另一笔交易时间差 ≤ 60 分钟。
    /// 思路：按交易名聚合，聚合时顺便两两比较时间与城市；
    /// 命中则把双方下标都标记为无效。最后按下标取出原始字符串。
    /// 时间 O(n · k)（k 为同名交易数），额外空间 O(n)。
    pub fn invalid_transactions(transactions: Vec<String>) -> Vec<String> {
        // name -> 该名字下已处理的 (时间, 城市, 下标)
        let mut by_name: HashMap<&str, Vec<(u32, &str, usize)>> = HashMap::new();
        let mut invalid: HashSet<usize> = HashSet::new();

        for (i, t) in transactions.iter().enumerate() {
            // 直接借用切片，避免 to_string 的多次堆分配
            let mut parts = t.split(',');
            let name = parts.next().unwrap();
            let time: u32 = parts.next().unwrap().parse().unwrap();
            let amount: u32 = parts.next().unwrap().parse().unwrap();
            let city = parts.next().unwrap();

            // 条件 1：金额超限
            if amount > 1000 {
                invalid.insert(i);
            }

            // 条件 2：与同名、异城、时间差 ≤ 60 的交易互为无效
            let same_name = by_name.entry(name).or_default();
            for &(t0, c0, i0) in same_name.iter() {
                if c0 != city && time.abs_diff(t0) <= 60 {
                    invalid.insert(i);
                    invalid.insert(i0);
                }
            }

            same_name.push((time, city, i));
        }

        invalid
            .into_iter()
            .map(|i| transactions[i].clone())
            .collect()
    }
}
```
