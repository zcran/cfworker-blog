---
title: "leetcode-哈希表90"
date: 2026-10-09T20:08:01+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 计算单个区间中出现的整数数量

给你一个整数数组 nums。

如果整数 x 在 nums 中的所有出现位置都位于同一个 连续 区间内，则称 x 为 特殊整数。

返回 nums 中 不同 特殊整数的数量。


```
use std::collections::HashMap;

impl Solution {
    pub fn count_special_integers(nums: Vec<i32>) -> i32 {
        // 记录每个值第一次和最后一次出现的下标，以及出现次数
        // (first, last, count)
        let mut stats: HashMap<i32, (usize, usize, usize)> = HashMap::new();

        for (i, &v) in nums.iter().enumerate() {
            let e = stats.entry(v).or_insert((i, i, 0));
            e.1 = i;
            e.2 += 1;
        }

        // 若 [first, last] 的长度恰好等于出现次数，说明所有出现都连续
        stats
            .values()
            .filter(|&&(first, last, count)| last - first + 1 == count)
            .count() as i32
    }
}
```
