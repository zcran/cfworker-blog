---
title: "leetcode-计数24"
date: 2026-08-01T10:40:52+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 找出数组中的幸运数

在整数数组中，如果一个整数的出现频次和它的数值大小相等，我们就称这个整数为「幸运数」。

给你一个整数数组 arr，请你从中找出并返回一个幸运数。

如果数组中存在多个幸运数，只需返回 最大 的那个。

如果数组中不含幸运数，则返回 -1 。


```
impl Solution {
    pub fn find_lucky(arr: Vec<i32>) -> i32 {
        // 统计每个数字出现的次数
        let mut count = std::collections::HashMap::new();
        for &num in &arr {
            *count.entry(num).or_insert(0) += 1;
        }

        // 找出最大的满足 "数值 == 出现次数" 的数字
        let mut lucky = -1;
        for (&num, &freq) in &count {
            if num == freq && num > lucky {
                lucky = num;
            }
        }

        lucky
    }
}
```
