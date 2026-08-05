---
title: "leetcode-计数50"
date: 2026-08-01T10:40:53+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 数组中第 K 个独一无二的字符串

独一无二的字符串 指的是在一个数组中只出现过 一次 的字符串。

给你一个字符串数组 arr 和一个整数 k ，请你返回 arr 中第 k 个 独一无二的字符串 。如果 少于 k 个独一无二的字符串，那么返回 空字符串 "" 。

注意，按照字符串在原数组中的 顺序 找到第 k 个独一无二字符串。


```
use std::collections::HashMap;

impl Solution {
    pub fn kth_distinct(arr: Vec<String>, k: i32) -> String {
        // 统计每个字符串出现的次数
        // 使用 &str 作为 key 避免克隆字符串
        let mut counts = HashMap::with_capacity(arr.len());
        for s in &arr {
            *counts.entry(s.as_str()).or_insert(0) += 1;
        }

        // 按原顺序查找第 k 个出现次数为 1 的字符串
        let mut found = 0;
        for s in &arr {
            if counts.get(s.as_str()) == Some(&1) {
                found += 1;
                if found == k {
                    return s.clone();
                }
            }
        }

        // 少于 k 个独一无二的字符串
        String::new()
    }
}
```
