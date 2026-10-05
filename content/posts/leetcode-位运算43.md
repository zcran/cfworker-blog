---
title: "leetcode-位运算43"
date: 2026-09-24T10:33:36+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 子字符串异或查询

给你一个 二进制字符串 s 和一个整数数组 queries ，其中 queries[i] = [firsti, secondi] 。

对于第 i 个查询，找到 s 的 最短子字符串 ，它对应的 十进制值 val 与 firsti 按位异或 得到 secondi ，换言之，val ^ firsti == secondi 。

第 i 个查询的答案是子字符串 [lefti, righti] 的两个端点（下标从 0 开始），如果不存在这样的子字符串，则答案为 [-1, -1] 。如果有多个答案，请你选择 lefti 最小的一个。

请你返回一个数组 ans ，其中 ans[i] = [lefti, righti] 是第 i 个查询的答案。

子字符串 是一个字符串中一段连续非空的字符序列。


```
use std::collections::HashMap;
use std::collections::HashSet;

impl Solution {
    pub fn substring_xor_queries(s: String, queries: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        // 目标值 val = first ^ second，只需在 s 的所有子串十进制值中找到 val 的最短区间。
        let targets: HashSet<i64> = queries.iter().map(|q| q[0] as i64 ^ q[1] as i64).collect();

        // 记录每个目标值对应的最短（left 最小）区间。
        let mut best: HashMap<i64, (i32, i32)> = HashMap::new();
        let bytes = s.as_bytes();
        let n = bytes.len();

        for left in 0..n {
            // 以 '0' 开头的子串值只可能是 0（且只取最短的单字符 "0"）。
            if bytes[left] == b'0' {
                if targets.contains(&0) {
                    best.entry(0).or_insert((left as i32, left as i32));
                }
                continue;
            }

            // 以 '1' 开头，逐位扩展计算子串十进制值，超过 i32 上限即可停止。
            let mut val: i64 = 0;
            for right in left..n {
                val = (val << 1) + (bytes[right] - b'0') as i64;
                if val > i32::MAX as i64 {
                    break;
                }
                if targets.contains(&val) {
                    // 首次记录即保证 left 最小，且同一 left 下 right 也最小。
                    best.entry(val).or_insert((left as i32, right as i32));
                }
            }
        }

        queries
            .iter()
            .map(|q| {
                let key = q[0] as i64 ^ q[1] as i64;
                match best.get(&key) {
                    Some(&(l, r)) => vec![l, r],
                    None => vec![-1, -1],
                }
            })
            .collect()
    }
}
```
