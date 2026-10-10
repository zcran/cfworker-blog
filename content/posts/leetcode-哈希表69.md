---
title: "leetcode-哈希表69"
date: 2026-10-09T20:08:00+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 查询数组中元素的出现位置

给你一个整数数组 nums ，一个整数数组 queries 和一个整数 x 。

对于每个查询 queries[i] ，你需要找到 nums 中第 queries[i] 个 x 的位置，并返回它的下标。如果数组中 x 的出现次数少于 queries[i] ，该查询的答案为 -1 。

请你返回一个整数数组 answer ，包含所有查询的答案。


```
impl Solution {
    pub fn occurrences_of_element(nums: Vec<i32>, queries: Vec<i32>, x: i32) -> Vec<i32> {
        // 记录 nums 中每个 x 出现的下标，按出现顺序排列
        let positions: Vec<i32> = nums
            .iter()
            .enumerate()
            .filter(|&(_, &v)| v == x)
            .map(|(i, _)| i as i32)
            .collect();

        // 第 q 个 x 对应 positions[q-1]；越界则返回 -1
        queries
            .iter()
            .map(|&q| {
                let idx = q as usize;
                if idx <= positions.len() {
                    positions[idx - 1]
                } else {
                    -1
                }
            })
            .collect()
    }
}
```
