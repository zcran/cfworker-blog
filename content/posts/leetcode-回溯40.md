---
title: "leetcode-回溯40"
date: 2026-07-04T10:22:03+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 黄金矿工

你要开发一座金矿，地质勘测学家已经探明了这座金矿中的资源分布，并用大小为 m * n 的网格 grid 进行了标注。每个单元格中的整数就表示这一单元格中的黄金数量；如果该单元格是空的，那么就是 0。

为了使收益最大化，矿工需要按以下规则来开采黄金：

每当矿工进入一个单元，就会收集该单元格中的所有黄金。
矿工每次可以从当前位置向上下左右四个方向走。
每个单元格只能被开采（进入）一次。
不得开采（进入）黄金数目为 0 的单元格。
矿工可以从网格中 任意一个 有黄金的单元格出发或者是停止。


```
impl Solution {
    pub fn get_maximum_gold(mut grid: Vec<Vec<i32>>) -> i32 {
        let (m, n) = (grid.len(), grid[0].len());
        let mut max_gold = 0;

        // 从每个有黄金的单元格出发尝试所有路径
        for i in 0..m {
            for j in 0..n {
                if grid[i][j] > 0 {
                    max_gold = max_gold.max(Self::dfs(&mut grid, i, j, m, n));
                }
            }
        }
        max_gold
    }

    /// 深度优先搜索：从当前位置出发能收集的最大黄金数
    fn dfs(grid: &mut Vec<Vec<i32>>, r: usize, c: usize, m: usize, n: usize) -> i32 {
        let gold = grid[r][c];
        grid[r][c] = 0; // 标记已访问

        let mut max_path = 0;
        // 四个方向：上、下、左、右
        let directions = [(-1, 0), (1, 0), (0, -1), (0, 1)];

        for (dr, dc) in directions {
            let nr = r as isize + dr;
            let nc = c as isize + dc;

            // 检查边界和黄金数量
            if nr >= 0 && nr < m as isize && nc >= 0 && nc < n as isize {
                let (nr, nc) = (nr as usize, nc as usize);
                if grid[nr][nc] > 0 {
                    max_path = max_path.max(Self::dfs(grid, nr, nc, m, n));
                }
            }
        }

        grid[r][c] = gold; // 回溯恢复
        gold + max_path // 当前黄金 + 后续路径最大收益
    }
}
```
