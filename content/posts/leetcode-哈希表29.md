---
title: "leetcode-哈希表29"
date: 2026-10-09T20:07:57+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 网格照明

在大小为 n x n 的网格 grid 上，每个单元格都有一盏灯，最初灯都处于 关闭 状态。

给你一个由灯的位置组成的二维数组 lamps ，其中 lamps[i] = [rowi, coli] 表示 打开 位于 grid[rowi][coli] 的灯。即便同一盏灯可能在 lamps 中多次列出，不会影响这盏灯处于 打开 状态。

当一盏灯处于打开状态，它将会照亮 自身所在单元格 以及同一 行 、同一 列 和两条 对角线 上的 所有其他单元格 。

另给你一个二维数组 queries ，其中 queries[j] = [rowj, colj] 。对于第 j 个查询，如果单元格 [rowj, colj] 是被照亮的，则查询结果为 1 ，否则为 0 。在第 j 次查询之后 [按照查询的顺序] ，关闭 位于单元格 grid[rowj][colj] 上及相邻 8 个方向上（与单元格 grid[rowi][coli] 共享角或边）的任何灯。

返回一个整数数组 ans 作为答案， ans[j] 应等于第 j 次查询 queries[j] 的结果，1 表示照亮，0 表示未照亮。


```
use std::collections::{HashMap, HashSet};

impl Solution {
    pub fn grid_illumination(n: i32, lamps: Vec<Vec<i32>>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        // 只需维护四类"灯计数"：行、列、主对角线（\）、副对角线（/）。
        // 查询时任一计数 > 0 即被照亮；随后关闭查询点周围 9 格内的灯并更新计数。
        // 用 HashSet 记录每个 (行,列) 上的灯，避免重复关闭。
        let n = n as i64;

        // 主对角线 x-y 为常量，副对角线 x+y 为常量（用 i64 防溢出）。
        let mut rows: HashMap<i64, i32> = HashMap::new();
        let mut cols: HashMap<i64, i32> = HashMap::new();
        let mut diag: HashMap<i64, i32> = HashMap::new(); // x - y
        let mut anti: HashMap<i64, i32> = HashMap::new(); // x + y
        let mut lamps_set: HashSet<(i64, i64)> = HashSet::new();

        for lamp in &lamps {
            let (x, y) = (lamp[0] as i64, lamp[1] as i64);
            if !lamps_set.insert((x, y)) {
                continue; // 同一位置的重复灯只计一次
            }
            *rows.entry(x).or_insert(0) += 1;
            *cols.entry(y).or_insert(0) += 1;
            *diag.entry(x - y).or_insert(0) += 1;
            *anti.entry(x + y).or_insert(0) += 1;
        }

        let mut ans = Vec::with_capacity(queries.len());
        for q in &queries {
            let (x, y) = (q[0] as i64, q[1] as i64);

            // 判断是否被照亮：四类计数任一大于 0。
            let lit = rows.get(&x).copied().unwrap_or(0) > 0
                || cols.get(&y).copied().unwrap_or(0) > 0
                || diag.get(&(x - y)).copied().unwrap_or(0) > 0
                || anti.get(&(x + y)).copied().unwrap_or(0) > 0;
            ans.push(lit as i32);

            // 关闭周围 9 格内的灯。
            for dx in -1..=1 {
                for dy in -1..=1 {
                    let (nx, ny) = (x + dx, y + dy);
                    if nx < 0 || nx >= n || ny < 0 || ny >= n {
                        continue;
                    }
                    if lamps_set.remove(&(nx, ny)) {
                        *rows.get_mut(&nx).unwrap() -= 1;
                        *cols.get_mut(&ny).unwrap() -= 1;
                        *diag.get_mut(&(nx - ny)).unwrap() -= 1;
                        *anti.get_mut(&(nx + ny)).unwrap() -= 1;
                    }
                }
            }
        }
        ans
    }
}
```
