---
title: "leetcode-贪心111"
date: 2026-10-03T12:55:41+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 重排水果

你有两个果篮，每个果篮中有 n 个水果。给你两个下标从 0 开始的整数数组 basket1 和 basket2 ，用以表示两个果篮中每个水果的交换成本。你想要让两个果篮相等。为此，可以根据需要多次执行下述操作：

选中两个下标 i 和 j ，并交换 basket1 中的第 i 个水果和 basket2 中的第 j 个水果。
交换的成本是 min(basket1i,basket2j) 。

根据果篮中水果的成本进行排序，如果排序后结果完全相同，则认为两个果篮相等。

返回使两个果篮相等的最小交换成本，如果无法使两个果篮相等，则返回 -1 。


```
use std::collections::HashMap;

impl Solution {
    pub fn min_cost(basket1: Vec<i32>, basket2: Vec<i32>) -> i64 {
        // 统计每个水果在两篮中的净出现次数，同时记录全局最小值 min_val
        let mut freq: HashMap<i32, i32> = HashMap::new();
        let mut min_val = i32::MAX;
        for &b in basket1.iter().chain(basket2.iter()) {
            min_val = min_val.min(b);
        }
        for &b in &basket1 {
            *freq.entry(b).or_insert(0) += 1;
        }
        for &b in &basket2 {
            *freq.entry(b).or_insert(0) -= 1;
        }

        // 若某成本出现总次数为奇数，则无法平分，返回 -1
        let mut excess = Vec::new();
        for (&val, &cnt) in &freq {
            if cnt % 2 != 0 {
                return -1;
            }
            // 需要从多的一侧交换出去的元素，各取一半
            for _ in 0..cnt.abs() / 2 {
                excess.push(val);
            }
        }

        // 贪心：排序后取前一半，每个元素要么直接交换（成本 val），
        // 要么借助全局最小值 min_val 做中转（成本 2 * min_val），取较小者
        excess.sort_unstable();
        let half = excess.len() / 2;
        excess[..half]
            .iter()
            .map(|&x| x.min(2 * min_val) as i64)
            .sum()
    }
}
```
