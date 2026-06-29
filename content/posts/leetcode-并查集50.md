---
title: "leetcode-并查集50"
date: 2026-06-25T11:22:23+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 网格图中鱼的最大数目

给你一个下标从 0 开始大小为 m x n 的二维整数数组 grid ，其中下标在 (r, c) 处的整数表示：

· 如果 grid[r][c] = 0 ，那么它是一块 陆地 。
· 如果 grid[r][c] > 0 ，那么它是一块 水域 ，且包含 grid[r][c] 条鱼。

一位渔夫可以从任意 水域 格子 (r, c) 出发，然后执行以下操作任意次：

· 捕捞格子 (r, c) 处所有的鱼，或者
· 移动到相邻的 水域 格子。

请你返回渔夫最优策略下， 最多 可以捕捞多少条鱼。如果没有水域格子，请你返回 0 。

格子 (r, c) 相邻 的格子为 (r, c + 1) ，(r, c - 1) ，(r + 1, c) 和 (r - 1, c) ，前提是相邻格子在网格图内。


```
impl Solution {
    pub fn find_max_fish(grid: Vec<Vec<i32>>) -> i32 {
        let m = grid.len();
        let n = grid[0].len();
        let mut visited = vec![vec![false; n]; m];
        let mut max_fish = 0;
        let directions = [(1, 0), (-1, 0), (0, 1), (0, -1)];

        for i in 0..m {
            for j in 0..n {
                // 跳过已访问或陆地
                if visited[i][j] || grid[i][j] == 0 {
                    continue;
                }

                // BFS 统计当前水域连通分量的总鱼数
                let mut stack = Vec::new();
                stack.push((i, j));
                visited[i][j] = true;
                let mut total_fish = 0;

                while let Some((r, c)) = stack.pop() {
                    total_fish += grid[r][c];

                    for (dr, dc) in directions {
                        let nr = r as isize + dr;
                        let nc = c as isize + dc;

                        // 检查边界、是否访问、是否为水域
                        if nr >= 0 && nr < m as isize && nc >= 0 && nc < n as isize {
                            let (nr, nc) = (nr as usize, nc as usize);
                            if !visited[nr][nc] && grid[nr][nc] > 0 {
                                visited[nr][nc] = true;
                                stack.push((nr, nc));
                            }
                        }
                    }
                }

                max_fish = max_fish.max(total_fish);
            }
        }

        max_fish
    }
}
```
