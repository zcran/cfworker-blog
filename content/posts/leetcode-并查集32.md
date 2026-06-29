---
title: "leetcode-并查集32"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 连接所有点的最小费用


给你一个points 数组，表示 2D 平面上的一些点，其中 points[i] = [xi, yi] 。

连接点 [xi, yi] 和点 [xj, yj] 的费用为它们之间的 曼哈顿距离 ：|xi - xj| + |yi - yj| ，其中 |val| 表示 val 的绝对值。

请你返回将所有点连接的最小总费用。只有任意两点之间 有且仅有 一条简单路径时，才认为所有点都已连接。

```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    /// 返回连接所有点的最小曼哈顿距离总费用。
    /// 使用 Prim 算法（堆优化）求最小生成树。
    pub fn min_cost_connect_points(points: Vec<Vec<i32>>) -> i32 {
        let n = points.len();
        if n <= 1 {
            return 0;
        }

        // 预处理点坐标，避免重复索引
        let pts: Vec<(i32, i32)> = points.iter().map(|p| (p[0], p[1])).collect();

        let mut visited = vec![false; n];
        let mut min_heap = BinaryHeap::new();
        let mut total_cost = 0;
        let mut connected = 0;

        // 从点 0 开始
        visited[0] = true;
        connected = 1;
        for i in 1..n {
            let cost = manhattan(pts[0], pts[i]);
            min_heap.push(Reverse((cost, i)));
        }

        while let Some(Reverse((cost, to))) = min_heap.pop() {
            if visited[to] {
                continue;
            }

            // 添加该点到生成树
            visited[to] = true;
            total_cost += cost;
            connected += 1;

            if connected == n {
                break;
            }

            // 将新点的所有未访问邻边加入堆
            for i in 0..n {
                if !visited[i] {
                    let new_cost = manhattan(pts[to], pts[i]);
                    min_heap.push(Reverse((new_cost, i)));
                }
            }
        }

        total_cost
    }
}

/// 计算曼哈顿距离
#[inline]
fn manhattan(p1: (i32, i32), p2: (i32, i32)) -> i32 {
    (p1.0 - p2.0).abs() + (p1.1 - p2.1).abs()
}
```
