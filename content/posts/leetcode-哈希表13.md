---
title: "leetcode-哈希表13"
date: 2026-10-09T20:07:55+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 砖墙

你的面前有一堵矩形的、由 n 行砖块组成的砖墙。这些砖块高度相同（也就是一个单位高）但是宽度不同。每一行砖块的宽度之和相等。

你现在要画一条 自顶向下 的、穿过 最少 砖块的垂线。如果你画的线只是从砖块的边缘经过，就不算穿过这块砖。你不能沿着墙的两个垂直边缘之一画线，这样显然是没有穿过一块砖的。

给你一个二维数组 wall ，该数组包含这堵墙的相关信息。其中，wall[i] 是一个代表从左至右每块砖的宽度的数组。你需要找出怎样画才能使这条线 穿过的砖块数量最少 ，并且返回 穿过的砖块数量 。


```
use std::collections::HashMap;

impl Solution {
    pub fn least_bricks(wall: Vec<Vec<i32>>) -> i32 {
        // 穿过砖块数 = 行数 - 穿过缝隙数（缝隙最多的位置最优）。
        // 每行最后一个前缀和是墙的右边缘，不能作为画线位置，故排除。
        // 砖宽之和可能超出 i32 范围，前缀和用 i64。
        let mut gaps: HashMap<i64, i32> = HashMap::new();
        let mut max_gaps = 0;
        for row in &wall {
            let mut pos: i64 = 0;
            // 只需累计到倒数第二块砖的右边缘。
            for &b in &row[..row.len() - 1] {
                pos += b as i64;
                let cnt = gaps.entry(pos).or_insert(0);
                *cnt += 1;
                max_gaps = max_gaps.max(*cnt);
            }
        }
        wall.len() as i32 - max_gaps
    }
}
```
