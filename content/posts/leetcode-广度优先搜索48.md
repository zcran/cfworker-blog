---
title: "leetcode-广度优先搜索48"
date: 2026-09-13T09:32:09+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 变换的迷宫

某解密游戏中，有一个 N*M 的迷宫，迷宫地形会随时间变化而改变，迷宫出口一直位于 (n-1,m-1) 位置。迷宫变化规律记录于 maze 中，maze[i] 表示 i 时刻迷宫的地形状态，"." 表示可通行空地，"#" 表示陷阱。

地形图初始状态记作 maze[0]，此时小力位于起点 (0,0)。此后每一时刻可选择往上、下、左、右其一方向走一步，或者停留在原地。

小力背包有以下两个魔法卷轴（卷轴使用一次后消失）：

临时消除术：将指定位置在下一个时刻变为空地；
永久消除术：将指定位置永久变为空地。

请判断在迷宫变化结束前（含最后时刻），小力能否在不经过任意陷阱的情况下到达迷宫出口呢？

注意： 输入数据保证起点和终点在所有时刻均为空地。


```
impl Solution {
    pub fn escape_maze(maze: Vec<Vec<String>>) -> bool {
        let max_step = maze.len() - 1;
        let n = maze[0].len();
        let m = maze[0][0].len();

        // 把每层地形转成字节网格，便于快速访问
        let grid: Vec<Vec<Vec<u8>>> = maze
            .iter()
            .map(|layer| layer.iter().map(|s| s.as_bytes().to_vec()).collect())
            .collect();

        // visited[x][y][step][magic1][magic2]
        // magic1: 临时消除术是否已用；magic2: 永久消除术是否已用
        let mut visited = vec![
            vec![
                vec![[[false; 2]; 2]; max_step + 1];
                m
            ];
            n
        ];

        const DIRS: [(isize, isize); 5] = [(0, 1), (1, 0), (0, -1), (-1, 0), (0, 0)];

        // DFS + 记忆化
        fn dfs(
            x: usize,
            y: usize,
            step: usize,
            magic1: bool,
            magic2: bool,
            n: usize,
            m: usize,
            max_step: usize,
            grid: &[Vec<Vec<u8>>],
            visited: &mut Vec<Vec<Vec<[[bool; 2]; 2]>>>,
        ) -> bool {
            if visited[x][y][step][magic1 as usize][magic2 as usize] {
                return false;
            }
            visited[x][y][step][magic1 as usize][magic2 as usize] = true;

            // 到达终点
            if x == n - 1 && y == m - 1 {
                return true;
            }
            // 时间用尽
            if step == max_step {
                return false;
            }
            // 剩余时间不足以走到终点（曼哈顿距离剪枝）
            if max_step - step < (n - 1 - x) + (m - 1 - y) {
                return false;
            }

            for &(dx, dy) in &DIRS {
                let nx = x as isize + dx;
                let ny = y as isize + dy;
                if nx < 0 || ny < 0 || nx >= n as isize || ny >= m as isize {
                    continue;
                }
                let (nx, ny) = (nx as usize, ny as usize);

                if grid[step + 1][nx][ny] == b'.' {
                    // 空地，直接走
                    if dfs(nx, ny, step + 1, magic1, magic2, n, m, max_step, grid, visited) {
                        return true;
                    }
                } else {
                    // 陷阱：使用临时消除术
                    if !magic1
                        && dfs(nx, ny, step + 1, true, magic2, n, m, max_step, grid, visited)
                    {
                        return true;
                    }
                    // 陷阱：使用永久消除术，选择从 step+1 到 max_step 的某一时刻踩过去
                    if !magic2 {
                        for s in step + 1..=max_step {
                            if dfs(nx, ny, s, magic1, true, n, m, max_step, grid, visited) {
                                return true;
                            }
                        }
                    }
                }
            }

            false
        }

        dfs(
            0, 0, 0, false, false, n, m, max_step, &grid, &mut visited,
        )
    }
}
```
