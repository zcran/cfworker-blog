---
title: "leetcode-广度优先搜索37"
date: 2026-09-13T09:32:08+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 迷宫中离入口最近的出口

给你一个 m x n 的迷宫矩阵 maze （下标从 0 开始），矩阵中有空格子（用 '.' 表示）和墙（用 '+' 表示）。同时给你迷宫的入口 entrance ，用 entrance = [entrancerow, entrancecol] 表示你一开始所在格子的行和列。

每一步操作，你可以往 上，下，左 或者 右 移动一个格子。你不能进入墙所在的格子，你也不能离开迷宫。你的目标是找到离 entrance 最近 的出口。出口 的含义是 maze 边界 上的 空格子。entrance 格子 不算 出口。

请你返回从 entrance 到最近出口的最短路径的 步数 ，如果不存在这样的路径，请你返回 -1 。


```
use std::collections::VecDeque;

impl Solution {
    pub fn nearest_exit(maze: Vec<Vec<char>>, entrance: Vec<i32>) -> i32 {
        let mut maze = maze;
        let rows = maze.len() as i32;
        let cols = maze[0].len() as i32;
        let start = (entrance[0], entrance[1]);

        // BFS 从入口出发，逐层扩展
        let mut queue = VecDeque::new();
        queue.push_back(start);
        maze[start.0 as usize][start.1 as usize] = '+'; // 标记入口已访问

        let mut steps = 0;
        const DIRS: [(i32, i32); 4] = [(-1, 0), (1, 0), (0, -1), (0, 1)];

        while !queue.is_empty() {
            // 逐层扩展，每层代表一步
            for _ in 0..queue.len() {
                let (x, y) = queue.pop_front().unwrap();

                // 当前格子位于边界且不是入口 → 找到最近出口
                if (x == 0 || x == rows - 1 || y == 0 || y == cols - 1) && (x, y) != start {
                    return steps;
                }

                // 向四个方向扩展
                for &(dx, dy) in &DIRS {
                    let nx = x + dx;
                    let ny = y + dy;
                    if nx >= 0 && nx < rows && ny >= 0 && ny < cols {
                        if maze[nx as usize][ny as usize] == '.' {
                            maze[nx as usize][ny as usize] = '+'; // 标记已访问
                            queue.push_back((nx, ny));
                        }
                    }
                }
            }
            steps += 1;
        }

        -1
    }
}
```
