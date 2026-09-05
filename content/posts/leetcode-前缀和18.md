---
title: "leetcode-前缀和18"
date: 2026-08-25T10:02:47+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 子数组异或查询

有一个正整数数组 arr，现给你一个对应的查询数组 queries，其中 queries[i] = [Li, Ri]。

对于每个查询 i，请你计算从 Li 到 Ri 的 XOR 值（即 arr[Li] xor arr[Li+1] xor ... xor arr[Ri]）作为本次查询的结果。

并返回一个包含给定查询 queries 所有结果的数组。


```
impl Solution {
    pub fn xor_queries(arr: Vec<i32>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        // 前缀异或数组：prefix[i] 表示 arr[0..i) 的异或值
        let mut prefix = vec![0; arr.len() + 1];
        for (i, &num) in arr.iter().enumerate() {
            prefix[i + 1] = prefix[i] ^ num;
        }

        // 每个查询结果 = prefix[R+1] ^ prefix[L]
        queries
            .into_iter()
            .map(|q| prefix[q[1] as usize + 1] ^ prefix[q[0] as usize])
            .collect()
    }
}
```
