---
title: "leetcode-哈希表15"
date: 2026-10-09T20:07:56+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 两个列表的最小索引总和

给定两个字符串数组 list1 和 list2，找到 索引和最小的公共字符串。

公共字符串 是同时出现在 list1 和 list2 中的字符串。

具有 最小索引和的公共字符串 是指，如果它在 list1[i] 和 list2[j] 中出现，那么 i + j 应该是所有其他 公共字符串 中的最小值。

返回所有 具有最小索引和的公共字符串。以 任何顺序 返回答案。


```
use std::collections::HashMap;

impl Solution {
    pub fn find_restaurant(list1: Vec<String>, list2: Vec<String>) -> Vec<String> {
        // 先把 list1 中每个字符串的最早索引存表（重复时保留更小索引）。
        // 再遍历 list2，用索引和 i+j 更新答案。
        let map: HashMap<&str, usize> = list1
            .iter()
            .enumerate()
            .map(|(i, s)| (s.as_str(), i))
            .collect();

        let mut min_sum = usize::MAX;
        let mut ans = Vec::new();
        for (i, s) in list2.iter().enumerate() {
            if let Some(&j) = map.get(s.as_str()) {
                let sum = i + j;
                match sum.cmp(&min_sum) {
                    std::cmp::Ordering::Less => {
                        min_sum = sum;
                        ans = vec![s.clone()];
                    }
                    std::cmp::Ordering::Equal => ans.push(s.clone()),
                    std::cmp::Ordering::Greater => {}
                }
            }
        }
        ans
    }
}
```
