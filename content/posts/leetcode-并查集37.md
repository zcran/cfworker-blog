---
title: "leetcode-并查集37"
date: 2026-06-25T11:22:23+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 你能穿过矩阵的最后一天

给你一个下标从 1 开始的二进制矩阵，其中 0 表示陆地，1 表示水域。同时给你 row 和 col 分别表示矩阵中行和列的数目。

一开始在第 0 天，整个 矩阵都是 陆地 。但每一天都会有一块新陆地被 水 淹没变成水域。给你一个下标从 1 开始的二维数组 cells ，其中 cells[i] = [ri, ci] 表示在第 i 天，第 ri 行 ci 列（下标都是从 1 开始）的陆地会变成 水域 （也就是 0 变成 1 ）。

你想知道从矩阵最 上面 一行走到最 下面 一行，且只经过陆地格子的 最后一天 是哪一天。你可以从最上面一行的 任意 格子出发，到达最下面一行的 任意 格子。你只能沿着 四个 基本方向移动（也就是上下左右）。

请返回只经过陆地格子能从最 上面 一行走到最 下面 一行的 最后一天 。


```
impl Solution {
    pub fn latest_day_to_cross(row: i32, col: i32, cells: Vec<Vec<i32>>) -> i32 {
        let row = row as usize;
        let col = col as usize;
        let total = row * col;
        let dirs = [(-1, 0), (1, 0), (0, -1), (0, 1)];

        // 二分查找：在 mid 天时，前 mid 个格子变成水域（1），其余是陆地（0）
        let mut left = 0;
        let mut right = total;
        let mut answer = 0;

        while left <= right {
            let mid = (left + right) / 2;

            // 构建第 mid 天的网格：前 mid 个为水域（1），其余为陆地（0）
            let mut grid = vec![vec![0; col]; row];
            for i in 0..mid {
                let r = (cells[i][0] - 1) as usize;
                let c = (cells[i][1] - 1) as usize;
                grid[r][c] = 1;
            }

            // BFS 从第一行所有陆地出发
            let mut queue = Vec::with_capacity(col);
            for c in 0..col {
                if grid[0][c] == 0 {
                    grid[0][c] = 1;  // 标记为已访问
                    queue.push((0, c));
                }
            }

            let mut can_reach = false;
            let mut head = 0;
            while head < queue.len() {
                let (x, y) = queue[head];
                head += 1;

                // 到达最后一行
                if x == row - 1 {
                    can_reach = true;
                    break;
                }

                for &(dx, dy) in &dirs {
                    let nx = x as i32 + dx;
                    let ny = y as i32 + dy;
                    if nx >= 0 && nx < row as i32 && ny >= 0 && ny < col as i32 {
                        let nx = nx as usize;
                        let ny = ny as usize;
                        if grid[nx][ny] == 0 {
                            grid[nx][ny] = 1;  // 标记为已访问
                            queue.push((nx, ny));
                        }
                    }
                }
            }

            if can_reach {
                answer = mid;
                left = mid + 1;   // 尝试更晚的天数
            } else {
                right = mid - 1;  // 尝试更早的天数
            }
        }

        answer as i32
    }
}
```
