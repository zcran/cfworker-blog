---
title: "leetcode-计数130"
date: 2026-08-01T10:40:58+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 数对和

设计一个算法，找出数组中两数之和为指定值的所有整数对。一个数只能属于一个数对。

```
use std::collections::HashMap;

impl Solution {
    /// 找出数组中所有和为 target 的数对，每个元素只能使用一次。
    ///
    /// 思路：先统计各数字出现频次，再遍历频次表。
    /// 对于每个数字 x，只处理 x <= target - x 的情况以避免重复，
    /// 配对数量为两数字频次的较小值（x 与自身配对时则为频次的一半）。
    pub fn pair_sums(nums: Vec<i32>, target: i32) -> Vec<Vec<i32>> {
        let mut cnt = HashMap::new();
        for n in nums {
            *cnt.entry(n).or_insert(0) += 1;
        }

        let mut res = Vec::new();
        for (&x, &n) in &cnt {
            let y = target - x;
            if x > y {
                continue; // 每个无序数对只处理一次
            }
            if let Some(&m) = cnt.get(&y) {
                let pairs = if x == y { n / 2 } else { n.min(m) };
                for _ in 0..pairs {
                    res.push(vec![x, y]);
                }
            }
        }
        res
    }
}
```
