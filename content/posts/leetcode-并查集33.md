---
title: "leetcode-并查集33"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 最小体力消耗路径

你准备参加一场远足活动。给你一个二维 rows x columns 的地图 heights ，其中 heights[row][col] 表示格子 (row, col) 的高度。一开始你在最左上角的格子 (0, 0) ，且你希望去最右下角的格子 (rows-1, columns-1) （注意下标从 0 开始编号）。你每次可以往 上，下，左，右 四个方向之一移动，你想要找到耗费 体力 最小的一条路径。

一条路径耗费的 体力值 是路径上相邻格子之间 高度差绝对值 的 最大值 决定的。

请你返回从左上角走到右下角的最小 体力消耗值 。


```
impl Solution {
    pub fn minimum_effort_path(heights: Vec<Vec<i32>>) -> i32 {
        use std::cmp::Reverse;
        use std::collections::BinaryHeap;

        let rows = heights.len();
        let cols = heights[0].len();
        let target = (rows - 1, cols - 1);

        // 方向数组：上、下、左、右
        let dirs = [(-1, 0), (1, 0), (0, -1), (0, 1)];

        // dist[r][c] 表示从起点到 (r,c) 的最小体力消耗值
        let mut dist = vec![vec![i32::MAX; cols]; rows];
        dist[0][0] = 0;

        // 最小堆：(当前路径的最大高度差, 行, 列)
        let mut heap = BinaryHeap::new();
        heap.push(Reverse((0, 0, 0)));

        while let Some(Reverse((effort, r, c))) = heap.pop() {
            // 如果已经到达终点，直接返回结果（因为堆保证了第一次弹出终点时是最小值）
            if (r, c) == target {
                return effort;
            }

            // 如果当前路径已经不是最优，跳过
            if effort > dist[r][c] {
                continue;
            }

            for (dr, dc) in dirs {
                let nr = r as isize + dr;
                let nc = c as isize + dc;

                // 检查边界
                if nr < 0 || nr >= rows as isize || nc < 0 || nc >= cols as isize {
                    continue;
                }

                let nr = nr as usize;
                let nc = nc as usize;

                // 计算从 (r,c) 到 (nr,nc) 的新的体力消耗值
                let new_effort = effort.max((heights[r][c] - heights[nr][nc]).abs());

                // 如果找到更优路径，更新并加入堆
                if new_effort < dist[nr][nc] {
                    dist[nr][nc] = new_effort;
                    heap.push(Reverse((new_effort, nr, nc)));
                }
            }
        }

        // 题目保证有路径，这里不会执行到
        unreachable!()
    }
}
```
