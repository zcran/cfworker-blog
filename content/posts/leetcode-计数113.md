---
title: "leetcode-计数113"
date: 2026-08-01T10:40:57+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 找到最常见的回答

给你一个二维字符串数组 responses，其中每个 responses[i] 是一个字符串数组，表示第 i 天调查的回答结果。

请返回在对每个 responses[i] 中的回答 去重 后，所有天数中 最常见 的回答。如果有多个回答出现频率相同，则返回 字典序最小 的那个回答。




```
use std::collections::{HashMap, HashSet};

impl Solution {
    /// 返回所有天数中去重后最常见的回答。
    /// 频率相同时返回字典序最小的回答。
    pub fn find_common_response(responses: Vec<Vec<String>>) -> String {
        let mut freq = HashMap::<String, u32>::new();
        let mut seen = HashSet::new();
        let mut best = String::new();
        let mut best_cnt = 0u32;

        for day in &responses {
            seen.clear();
            for s in day {
                // 当天去重：已出现过则跳过
                if !seen.insert(s.as_str()) {
                    continue;
                }
                // 累计全局频率
                let cnt = freq.entry(s.clone()).or_insert(0);
                *cnt += 1;
                // 更新最优解：频率更高，或频率相同但字典序更小
                if *cnt > best_cnt || (*cnt == best_cnt && s.as_str() < best.as_str()) {
                    best_cnt = *cnt;
                    best.clone_from(s); // 复用已有内存，避免重新分配
                }
            }
        }

        best
    }
}
```
