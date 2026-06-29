---
title: "leetcode-单调栈39"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 网格图中最少访问的格子数

给你一个下标从 0 开始的 m x n 整数矩阵 grid 。你一开始的位置在 左上角 格子 (0, 0) 。

当你在格子 (i, j) 的时候，你可以移动到以下格子之一：

满足 j < k <= grid[i][j] + j 的格子 (i, k) （向右移动），或者
满足 i < k <= grid[i][j] + i 的格子 (k, j) （向下移动）。

请你返回到达 右下角 格子 (m - 1, n - 1) 需要经过的最少移动格子数，如果无法到达右下角格子，请你返回 -1 。

```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    /// 最小访问格子数
    ///
    /// 从 (0,0) 出发，每个格子可以向右/下跳 grid[i][j] 步
    /// 求到达 (m-1, n-1) 的最少步数
    pub fn minimum_visited_cells(grid: Vec<Vec<i32>>) -> i32 {
        let m = grid.len();
        let n = grid[0].len();

        // dist[i][j] = 到达 (i,j) 的最小步数，-1 表示不可达
        let mut dist = vec![vec![-1; n]; m];
        dist[0][0] = 1;

        // 单调堆：存储 (距离, 位置)，按距离升序
        let mut row_pq: Vec<BinaryHeap<(Reverse<i32>, usize)>> = vec![BinaryHeap::new(); m];
        let mut col_pq: Vec<BinaryHeap<(Reverse<i32>, usize)>> = vec![BinaryHeap::new(); n];

        for i in 0..m {
            for j in 0..n {
                // 尝试从左边跳过来
                Self::update_dist(&mut dist[i][j], &mut row_pq[i], j, &grid, i, true);
                // 尝试从上面跳过来
                Self::update_dist(&mut dist[i][j], &mut col_pq[j], i, &grid, j, false);

                // 如果当前格子可达，加入优先队列供后续格子使用
                if dist[i][j] != -1 {
                    row_pq[i].push((Reverse(dist[i][j]), j));
                    col_pq[j].push((Reverse(dist[i][j]), i));
                }
            }
        }

        dist[m - 1][n - 1]
    }

    /// 从堆中取出可达的最优位置更新当前距离
    #[inline]
    fn update_dist(
        curr_dist: &mut i32,
        heap: &mut BinaryHeap<(Reverse<i32>, usize)>,
        curr_pos: usize,
        grid: &Vec<Vec<i32>>,
        fixed_idx: usize,
        is_row: bool,
    ) {
        // 移除所有无法跳到当前位置的记录
        while let Some(&(_, pos)) = heap.peek() {
            let max_reach = if is_row {
                pos + grid[fixed_idx][pos] as usize
            } else {
                pos + grid[pos][fixed_idx] as usize
            };

            if max_reach >= curr_pos {
                break;
            }
            heap.pop();
        }

        // 如果存在可达记录，更新距离
        if let Some(&(Reverse(d), _)) = heap.peek() {
            let new_dist = d + 1;
            if *curr_dist == -1 || new_dist < *curr_dist {
                *curr_dist = new_dist;
            }
        }
    }
}
```
