---
title: "leetcode-堆64"
date: 2026-08-21T09:54:14+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 选择不同 X 值三元组使 Y 值之和最大

给你两个整数数组 x 和 y，长度均为 n。你必须选择三个 不同 的下标 i ，j 和 k，满足以下条件：

x[i] != x[j]
x[j] != x[k]
x[k] != x[i]

你的目标是在满足这些条件下 最大化 y[i] + y[j] + y[k] 的值。返回通过选择这样一组三元组下标所能获得的 最大 可能和。

如果不存在这样的三元组，返回 -1。


```
use std::collections::HashMap;

impl Solution {
    pub fn max_sum_distinct_triplet(x: Vec<i32>, y: Vec<i32>) -> i32 {
        let mut best: HashMap<i32, i32> = HashMap::new();
        for (&xi, &yi) in x.iter().zip(y.iter()) {
            best.entry(xi)
                .and_modify(|v: &mut i32| *v = (*v).max(yi))
                .or_insert(yi);
        }

        if best.len() < 3 {
            return -1;
        }

        let mut vals: Vec<i32> = best.into_values().collect();
        let n = vals.len();
        // 升序找第3大的位置，该位置及之后即为最大的3个
        vals.select_nth_unstable(n - 3);
        vals[n - 3..].iter().sum()
    }
}
```
