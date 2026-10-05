---
title: "leetcode-位运算50"
date: 2026-09-24T10:33:37+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 统计距离为 k 的点对

给你一个 二维 整数数组 coordinates 和一个整数 k ，其中 coordinates[i] = [xi, yi] 是第 i 个点在二维平面里的坐标。

我们定义两个点 (x1, y1) 和 (x2, y2) 的 距离 为 (x1 XOR x2) + (y1 XOR y2) ，XOR 指的是按位异或运算。

请你返回满足 i < j 且点 i 和点 j之间距离为 k 的点对数目。


```
use std::collections::HashMap;

impl Solution {
    pub fn count_pairs(coordinates: Vec<Vec<i32>>, k: i32) -> i32 {
        // 距离 = (x1 ^ x2) + (y1 ^ y2) = k。
        // 设 dx = x1 ^ x2，dy = y1 ^ y2，则 dx + dy = k，枚举 dx ∈ [0, k] 即可。
        // 对当前点 p，只需查询之前是否存在点 q 满足 q.x = p.x ^ dx 且 q.y = p.y ^ dy。
        let mut ans = 0;
        let mut cnt: HashMap<i64, i32> = HashMap::new();

        for p in &coordinates {
            for dx in 0..=k {
                let dy = k - dx;
                // 用一个 long long 键把 (x, y) 压缩成唯一整数，避免元组哈希开销。
                let key = ((p[0] ^ dx) as i64) << 32 | ((p[1] ^ dy) as i64 & 0xFFFF_FFFF);
                if let Some(&c) = cnt.get(&key) {
                    ans += c;
                }
            }
            let key = (p[0] as i64) << 32 | (p[1] as i64 & 0xFFFF_FFFF);
            *cnt.entry(key).or_insert(0) += 1;
        }

        ans
    }
}
```
