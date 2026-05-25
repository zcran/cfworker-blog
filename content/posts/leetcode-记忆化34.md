---
title: "leetcode-记忆化34"
date: 2026-05-06T20:22:34+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 最长 V 形对角线段的长度

给你一个大小为 n x m 的二维整数矩阵 grid，其中每个元素的值为 0、1 或 2。

V 形对角线段 定义如下：

· 线段从 1 开始。
· 后续元素按照以下无限序列的模式排列：2, 0, 2, 0, ...。
· 该线段：
  · 起始于某个对角方向（左上到右下、右下到左上、右上到左下或左下到右上）。
  · 沿着相同的对角方向继续，保持 序列模式 。
  · 在保持 序列模式 的前提下，最多允许 一次顺时针 90 度转向 另一个对角方向。

```
impl Solution {
    /// 计算最长的“V 形对角线”的长度。
    ///
    /// 从值为 1 的格子出发，沿对角线方向移动，要求下一步的值为 2，再下一步为 0，再下一步为 2 ……
    /// 允许最多一次顺时针旋转 90 度（形成 V 形）。求能经过的最多格子数。
    pub fn len_of_v_diagonal(grid: Vec<Vec<i32>>) -> i32 {
        let m = grid.len();
        let n = grid[0].len();
        // 四个对角线方向：(行增量, 列增量)
        const DIRS: [(i32, i32); 4] = [(1, 1), (1, -1), (-1, -1), (-1, 1)];

        // 记忆化数组：memo[x][y][dir][turned] = 从 (x,y) 出发沿方向 dir，
        // 且是否已经转过弯（turned）为 turned 时能获得的最大步数（不包含当前格子）。
        // 初始值 -1 表示未计算。
        type Memo = Vec<Vec<Vec<Vec<i32>>>>;
        let mut memo: Memo = vec![vec![vec![vec![-1; 2]; 4]; n]; m];

        /// 深度优先搜索（记忆化递归）
        /// 返回从 (x, y) 出发，下一步目标值为 target，当前方向为 dir，
        /// 是否已转过弯为 turned 时，能继续走的最长长度（不包含当前格子）。
        fn dfs(
            x: usize,
            y: usize,
            dir: usize,
            turned: bool,
            target: i32,
            grid: &[Vec<i32>],
            memo: &mut Memo,
        ) -> i32 {
            let (dx, dy) = DIRS[dir];
            let nx = x as i32 + dx;
            let ny = y as i32 + dy;

            // 边界检查及下一个格子的值检查
            if nx < 0 || ny < 0 {
                return 0;
            }
            let (nx, ny) = (nx as usize, ny as usize);
            if nx >= grid.len() || ny >= grid[0].len() || grid[nx][ny] != target {
                return 0;
            }

            let turned_idx = turned as usize;
            // 已计算过，直接返回
            if memo[nx][ny][dir][turned_idx] != -1 {
                return memo[nx][ny][dir][turned_idx];
            }

            let next_target = 2 - target; // 交替 2 -> 0 -> 2 -> 0 ...
            // 1. 继续直走
            let mut best = dfs(nx, ny, dir, turned, next_target, grid, memo);
            // 2. 如果允许转向，尝试顺时针旋转 90 度（转向后不可再转）
            if turned {
                let new_dir = (dir + 1) % 4;
                best = best.max(dfs(nx, ny, new_dir, false, next_target, grid, memo));
            }
            let res = best + 1; // 加上从 (nx, ny) 到后续的这条边
            memo[nx][ny][dir][turned_idx] = res;
            res
        }

        // 主逻辑：枚举所有值为 1 的格子作为起点，尝试四个初始方向
        (0..m)
            .flat_map(|i| (0..n).map(move |j| (i, j)))
            .filter(|&(i, j)| grid[i][j] == 1)
            .flat_map(|(i, j)| (0..4).map(move |dir| (i, j, dir)))
            .map(|(i, j, dir)| dfs(i, j, dir, true, 2, &grid, &mut memo) + 1) // +1 计入起点本身
            .max()
            .unwrap_or(0)
    }
}
```
