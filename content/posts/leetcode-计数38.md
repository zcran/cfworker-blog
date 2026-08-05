---
title: "leetcode-计数38"
date: 2026-08-01T10:40:52+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 唯一元素的和

给你一个整数数组 nums 。数组中唯一元素是那些只出现 恰好一次 的元素。

请你返回 nums 中唯一元素的 和 。




```
impl Solution {
    pub fn sum_of_unique(nums: Vec<i32>) -> i32 {
        use std::collections::HashMap;
        let mut count = HashMap::new();

        // 统计每个元素出现次数
        for &num in &nums {
            *count.entry(num).or_insert(0) += 1;
        }

        // 累加出现次数为1的元素
        count
            .iter()
            .filter(|(_, cnt)| **cnt == 1)  // 解引用两次
            .map(|(&num, _)| num)
            .sum()
    }
}
```
