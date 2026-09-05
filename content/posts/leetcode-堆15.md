---
title: "leetcode-堆15"
date: 2026-08-21T09:54:11+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 推箱子

「推箱子」是一款风靡全球的益智小游戏，玩家需要将箱子推到仓库中的目标位置。

游戏地图用大小为 m x n 的网格 grid 表示，其中每个元素可以是墙、地板或者是箱子。

现在你将作为玩家参与游戏，按规则将箱子 'B' 移动到目标位置 'T' ：

玩家用字符 'S' 表示，只要他在地板上，就可以在网格中向上、下、左、右四个方向移动。
地板用字符 '.' 表示，意味着可以自由行走。
墙用字符 '#' 表示，意味着障碍物，不能通行。
箱子仅有一个，用字符 'B' 表示。相应地，网格上有一个目标位置 'T'。
玩家需要站在箱子旁边，然后沿着箱子的方向进行移动，此时箱子会被移动到相邻的地板单元格。记作一次「推动」。
玩家无法越过箱子。

返回将箱子推到目标位置的最小 推动 次数，如果无法做到，请返回 -1。


```
use std::collections::VecDeque;

impl Solution {
    /// 0-1 BFS：状态为 (玩家位置, 箱子位置)，推动代价为 1，移动代价为 0
    pub fn min_push_box(grid: Vec<Vec<char>>) -> i32 {
        let (m, n) = (grid.len(), grid[0].len());

        // 找到玩家、箱子、目标位置
        let (mut px, mut py, mut bx, mut by, mut tx, mut ty) = (0, 0, 0, 0, 0, 0);
        for i in 0..m {
            for j in 0..n {
                match grid[i][j] {
                    'S' => (px, py) = (i, j),
                    'B' => (bx, by) = (i, j),
                    'T' => (tx, ty) = (i, j),
                    _ => {}
                }
            }
        }

        // 边界检查：在网格内且不是墙
        let in_bounds = |x: i32, y: i32| x >= 0 && x < m as i32 && y >= 0 && y < n as i32 && grid[x as usize][y as usize] != '#';

        // 访问标记：visit[px][py][bx][by] 表示该状态是否已访问
        let mut visit = vec![vec![vec![vec![false; n]; m]; n]; m];
        visit[px][py][bx][by] = true;

        // 0-1 BFS：推动箱子 push_back（代价+1），单纯移动 push_front（代价不变）
        let mut deque = VecDeque::from([(px, py, bx, by, 0)]);
        const DIRS: [(i32, i32); 4] = [(-1, 0), (1, 0), (0, -1), (0, 1)];

        while let Some((px, py, bx, by, pushes)) = deque.pop_front() {
            for &(dx, dy) in &DIRS {
                let npx = px as i32 + dx;
                let npy = py as i32 + dy;

                if !in_bounds(npx, npy) {
                    continue;
                }
                let (npx, npy) = (npx as usize, npy as usize);

                // 玩家移动到箱子位置 -> 推动箱子
                if npx == bx && npy == by {
                    let nbx = bx as i32 + dx;
                    let nby = by as i32 + dy;
                    if !in_bounds(nbx, nby) {
                        continue;
                    }
                    let (nbx, nby) = (nbx as usize, nby as usize);

                    // 到达目标
                    if nbx == tx && nby == ty {
                        return pushes + 1;
                    }
                    if visit[npx][npy][nbx][nby] {
                        continue;
                    }
                    visit[npx][npy][nbx][nby] = true;
                    deque.push_back((npx, npy, nbx, nby, pushes + 1));
                }
                // 玩家单纯移动，不推动箱子
                else if !visit[npx][npy][bx][by] {
                    visit[npx][npy][bx][by] = true;
                    deque.push_front((npx, npy, bx, by, pushes));
                }
            }
        }

        -1
    }
}
```
