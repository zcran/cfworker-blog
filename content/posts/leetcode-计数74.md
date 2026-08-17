---
title: "leetcode-计数74"
date: 2026-08-01T10:40:55+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 出现最频繁的偶数元素

给你一个整数数组 nums ，返回出现最频繁的偶数元素。

如果存在多个满足条件的元素，只需要返回 最小 的一个。如果不存在这样的元素，返回 -1 。


```
impl Solution {
    pub fn most_frequent_even(nums: Vec<i32>) -> i32 {
        // 使用 HashMap 统计偶数出现次数（效率高于 BTreeMap）
        use std::collections::HashMap;

        let mut freq = HashMap::new();
        for &num in &nums {
            if num % 2 == 0 {
                *freq.entry(num).or_insert(0) += 1;
            }
        }

        // 如果没有任何偶数，返回 -1
        if freq.is_empty() {
            return -1;
        }

        // 找出出现次数最多且值最小的偶数
        // 由于需要值最小，先按次数降序，再按键值升序
        freq.into_iter()
            .max_by(|a, b| a.1.cmp(&b.1).then_with(|| b.0.cmp(&a.0)))
            .map(|(num, _)| num)
            .unwrap()
    }
}
```
