---
title: "leetcode-哈希表33"
date: 2026-10-09T20:07:57+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 独一无二的出现次数

给你一个整数数组 arr，如果每个数的出现次数都是独一无二的，就返回 true；否则返回 false。


```
impl Solution {
    pub fn unique_occurrences(arr: Vec<i32>) -> bool {
        // 统计每个数的出现次数，再检查这些次数是否互不相同。
        // 用 HashSet 判断重复，无需排序，O(n)。
        use std::collections::{HashMap, HashSet};

        let mut freq: HashMap<i32, u32> = HashMap::new();
        for &x in &arr {
            *freq.entry(x).or_insert(0) += 1;
        }

        let mut seen = HashSet::new();
        freq.values().all(|&c| seen.insert(c))
    }
}
```
