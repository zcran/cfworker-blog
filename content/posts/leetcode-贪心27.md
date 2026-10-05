---
title: "leetcode-贪心27"
date: 2026-10-03T12:55:35+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 二倍数对数组

给定一个长度为偶数的整数数组 arr，只有对 arr 进行重组后可以满足 “对于每个 0 <= i < len(arr) / 2，都有 arr[2 * i + 1] = 2 * arr[2 * i]” 时，返回 true；否则，返回 false。


```
use std::collections::HashMap;

impl Solution {
    pub fn can_reorder_doubled(mut arr: Vec<i32>) -> bool {
        // 统计每个数字的出现次数
        let mut count: HashMap<i32, i32> = HashMap::new();
        for &n in &arr {
            *count.entry(n).or_insert(0) += 1;
        }

        // 0 只能和 0 配对，因此个数必须为偶数
        if let Some(&c) = count.get(&0) {
            if c % 2 != 0 {
                return false;
            }
            count.remove(&0);
        }

        // 按绝对值升序处理：保证总是先处理 x，再处理 2x
        arr.sort_unstable_by_key(|n| n.abs());

        for n in arr {
            if n == 0 {
                continue;
            }
            let c = *count.get(&n).unwrap_or(&0);
            if c == 0 {
                continue;
            }
            // 当前数字 n 需要与 2n 配对，2n 的数量必须足够
            let target = n * 2;
            match count.get_mut(&target) {
                Some(v) if *v >= c => {
                    *v -= c;
                    count.insert(n, 0);
                }
                _ => return false,
            }
        }

        true
    }
}
```
