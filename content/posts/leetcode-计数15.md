---
title: "leetcode-计数15"
date: 2026-08-01T10:40:51+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 受标签影响的最大值

以两个整数数组  values 和 labels 给定 n 个项的值和标签，并且给出两个整数 numWanted 和 useLimit 。

你的任务是从这些项中找到一个值的和 最大 的子集使得：

项的数量 最多 为 numWanted。

相同标签的项的数量 最多 为 useLimit。

返回最大的和。




```
impl Solution {
    pub fn largest_vals_from_labels(values: Vec<i32>, labels: Vec<i32>, num_wanted: i32, use_limit: i32) -> i32 {
        use std::collections::HashMap;

        // 将值和标签配对，并按值降序排列（从大到小）
        let mut items: Vec<_> = values.into_iter().zip(labels).collect();
        items.sort_unstable_by(|a, b| b.0.cmp(&a.0)); // 按值降序

        let mut label_count = HashMap::new();
        let mut total = 0;
        let mut taken = 0;

        for (value, label) in items {
            // 如果已达到最大选择数量，停止
            if taken >= num_wanted {
                break;
            }

            // 获取当前标签已选数量
            let count = label_count.entry(label).or_insert(0);

            // 如果该标签已达到使用限制，跳过
            if *count >= use_limit {
                continue;
            }

            // 选择当前项
            total += value;
            *count += 1;
            taken += 1;
        }

        total
    }
}
```
