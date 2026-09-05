---
title: "leetcode-堆40"
date: 2026-08-21T09:54:13+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 价格范围内最高排名的 K 样物品

给你一个下标从 0 开始的二维整数数组 grid ，它的大小为 m x n ，表示一个商店中物品的分布图。数组中的整数含义为：

· 0 表示无法穿越的一堵墙。
· 1 表示可以自由通过的一个空格子。
· 所有其他正整数表示该格子内的一样物品的价格。你可以自由经过这些格子。

从一个格子走到上下左右相邻格子花费 1 步。

同时给你一个整数数组 pricing 和 start ，其中 pricing = [low, high] 且 start = [row, col] ，表示你开始位置为 (row, col) ，同时你只对物品价格在 闭区间 [low, high] 之内的物品感兴趣。同时给你一个整数 k 。

你想知道给定范围 内 且 排名最高 的 k 件物品的 位置 。排名按照优先级从高到低的以下规则制定：

1. 距离：定义为从 start 到一件物品的最短路径需要的步数（较近 距离的排名更高）。
2. 价格：较低 价格的物品有更高优先级，但只考虑在给定范围之内的价格。
3. 行坐标：较小 行坐标的有更高优先级。
4. 列坐标：较小 列坐标的有更高优先级。

请你返回给定价格内排名最高的 k 件物品的坐标，将它们按照排名排序后返回。如果给定价格内少于 k 件物品，那么请将它们的坐标 全部 返回。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    pub fn highest_ranked_k_items(grid: Vec<Vec<i32>>, pricing: Vec<i32>, start: Vec<i32>, k: i32) -> Vec<Vec<i32>> {
        let mut grid = grid;
        let (low, high) = (pricing[0], pricing[1]);
        let rows = grid.len();
        let cols = grid[0].len();
        let k = k as usize;

        let (sx, sy) = (start[0] as usize, start[1] as usize);
        if grid[sx][sy] == 0 {
            return vec![];
        }

        // Min-heap: (distance, price, row, col)
        let mut heap: BinaryHeap<Reverse<(i32, i32, usize, usize)>> = BinaryHeap::new();
        heap.push(Reverse((0, grid[sx][sy], sx, sy)));
        grid[sx][sy] = 0; // mark visited

        let dirs = [(0, 1), (0, -1), (1, 0), (-1, 0)];
        let mut ans = Vec::with_capacity(k);

        while let Some(Reverse((dist, price, x, y))) = heap.pop() {
            if low <= price && price <= high {
                ans.push(vec![x as i32, y as i32]);
                if ans.len() >= k {
                    break;
                }
            }

            for (dx, dy) in dirs {
                let nx = x as i32 + dx;
                let ny = y as i32 + dy;
                if nx < 0 || ny < 0 || nx >= rows as i32 || ny >= cols as i32 {
                    continue;
                }
                let (nx, ny) = (nx as usize, ny as usize);
                if grid[nx][ny] == 0 {
                    continue;
                }
                heap.push(Reverse((dist + 1, grid[nx][ny], nx, ny)));
                grid[nx][ny] = 0; // mark visited
            }
        }

        ans
    }
}
```
