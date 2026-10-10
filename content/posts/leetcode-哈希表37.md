---
title: "leetcode-哈希表37"
date: 2026-10-09T20:07:57+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 收藏清单

给你一个数组 favoriteCompanies ，其中 favoriteCompanies[i] 是第 i 名用户收藏的公司清单（下标从 0 开始）。

请找出不是其他任何人收藏的公司清单的子集的收藏清单，并返回该清单下标。下标需要按升序排列。


```
use std::collections::HashSet;

impl Solution {
    pub fn people_indexes(favorite_companies: Vec<Vec<String>>) -> Vec<i32> {
        // 把每个用户的收藏转为 HashSet，便于 O(1) 判断包含关系。
        let sets: Vec<HashSet<&str>> = favorite_companies
            .iter()
            .map(|list| list.iter().map(|s| s.as_str()).collect())
            .collect();

        // 对每个 i，检查是否存在 j != i 使 i 是 j 的子集；
        // 若不存在，则 i 不是任何人的子集，加入答案（按 i 升序自然有序）。
        (0..sets.len())
            .filter(|&i| {
                !sets.iter().enumerate().any(|(j, sj)| {
                    j != i && sets[i].iter().all(|c| sj.contains(c))
                })
            })
            .map(|i| i as i32)
            .collect()
    }
}
```
