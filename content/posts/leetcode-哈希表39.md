---
title: "leetcode-哈希表39"
date: 2026-10-09T20:07:57+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 判断路径是否相交

给你一个字符串 path，其中 path[i] 的值可以是 'N'、'S'、'E' 或者 'W'，分别表示向北、向南、向东、向西移动一个单位。

你从二维平面上的原点 (0, 0) 处开始出发，按 path 所指示的路径行走。

如果路径在任何位置上与自身相交，也就是走到之前已经走过的位置，请返回 true ；否则，返回 false 。




```
use std::collections::HashSet;

impl Solution {
    pub fn is_path_crossing(path: String) -> bool {
        // 记录走过的所有坐标（含起点），每步移动后检查是否重复到达。
        let mut visited = HashSet::with_capacity(path.len() + 1);
        let (mut x, mut y) = (0i32, 0i32);
        visited.insert((x, y));

        for c in path.bytes() {
            // 用元组增量替代分支：N/S 改 y，E/W 改 x。
            match c {
                b'N' => y += 1,
                b'S' => y -= 1,
                b'E' => x += 1,
                _ => x -= 1, // b'W'
            }
            if !visited.insert((x, y)) {
                return true;
            }
        }
        false
    }
}
```
