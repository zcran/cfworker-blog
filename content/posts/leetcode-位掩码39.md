---
title: "leetcode-位掩码39"
date: 2026-05-18T10:28:00+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 寻宝

我们得到了一副藏宝图，藏宝图显示，在一个迷宫中存在着未被世人发现的宝藏。

迷宫是一个二维矩阵，用一个字符串数组表示。它标识了唯一的入口（用 'S' 表示），和唯一的宝藏地点（用 'T' 表示）。但是，宝藏被一些隐蔽的机关保护了起来。在地图上有若干个机关点（用 'M' 表示），只有所有机关均被触发，才可以拿到宝藏。

要保持机关的触发，需要把一个重石放在上面。迷宫中有若干个石堆（用 'O' 表示），每个石堆都有无限个足够触发机关的重石。但是由于石头太重，我们一次只能搬一个石头到指定地点。

迷宫中同样有一些墙壁（用 '#' 表示），我们不能走入墙壁。剩余的都是可随意通行的点（用 '.' 表示）。石堆、机关、起点和终点（无论是否能拿到宝藏）也是可以通行的。

我们每步可以选择向上/向下/向左/向右移动一格，并且不能移出迷宫。搬起石头和放下石头不算步数。那么，从起点开始，我们最少需要多少步才能最后拿到宝藏呢？如果无法拿到宝藏，返回 -1 。


```
use std::collections::VecDeque;

type Pos = (usize, usize);
type Grid = Vec<Vec<bool>>;          // true 可通行
type Dist = Vec<Vec<Option<u32>>>;   // None 表示不可达

impl Solution {
    /// BFS 计算从起点到所有格子的最短距离
    fn bfs_dist(grid: &Grid, start: Pos) -> Dist {
        let rows = grid.len();
        let cols = grid[0].len();
        let mut dist = vec![vec![None; cols]; rows];
        let mut q = VecDeque::new();
        dist[start.0][start.1] = Some(0);
        q.push_back(start);
        while let Some((x, y)) = q.pop_front() {
            let d = dist[x][y].unwrap();
            for (dx, dy) in &[(-1, 0), (1, 0), (0, -1), (0, 1)] {
                let nx = x as i32 + dx;
                let ny = y as i32 + dy;
                if nx >= 0 && nx < rows as i32 && ny >= 0 && ny < cols as i32 {
                    let (nx, ny) = (nx as usize, ny as usize);
                    if grid[nx][ny] && dist[nx][ny].is_none() {
                        dist[nx][ny] = Some(d + 1);
                        q.push_back((nx, ny));
                    }
                }
            }
        }
        dist
    }

    /// 解析迷宫，返回 (网格, 起点, 终点, 机关列表, 石头列表)
    fn parse_maze(maze: Vec<String>) -> (Grid, Pos, Pos, Vec<Pos>, Vec<Pos>) {
        let mut stones = Vec::new();
        let mut traps = Vec::new();
        let mut start = None;
        let mut target = None;
        let grid: Grid = maze
            .into_iter()
            .enumerate()
            .map(|(i, row)| {
                row.chars()
                    .enumerate()
                    .map(|(j, c)| match c {
                        '#' => false,
                        _ => {
                            match c {
                                'S' => start = Some((i, j)),
                                'T' => target = Some((i, j)),
                                'O' => stones.push((i, j)),
                                'M' => traps.push((i, j)),
                                _ => {}
                            }
                            true
                        }
                    })
                    .collect()
            })
            .collect();
        (
            grid,
            start.expect("No start"),
            target.expect("No target"),
            traps,
            stones,
        )
    }

    /// 从两个距离矩阵中，找出经过任意一个石头的最小总距离 (dist1[s] + dist2[s])
    fn min_via_stone(stones: &[Pos], dist1: &Dist, dist2: &Dist) -> Option<u32> {
        stones
            .iter()
            .filter_map(|&(x, y)| Some(dist1[x][y]? + dist2[x][y]?))
            .min()
    }

    pub fn minimal_steps(maze: Vec<String>) -> i32 {
        let (grid, start, target, traps, stones) = Self::parse_maze(maze);
        let t = traps.len();

        // 没有机关：直接走到终点
        if t == 0 {
            return Self::bfs_dist(&grid, start)[target.0][target.1]
                .map(|d| d as i32)
                .unwrap_or(-1);
        }
        // 有机关但没有石头：无法触发任何机关
        if stones.is_empty() {
            return -1;
        }

        // 预计算起点、终点、每个机关的 BFS 距离矩阵
        let dist_start = Self::bfs_dist(&grid, start);
        let dist_target = Self::bfs_dist(&grid, target);
        let dist_traps: Vec<Dist> = traps.iter().map(|&p| Self::bfs_dist(&grid, p)).collect();

        // 辅助检查：若距离为 None，则返回 -1
        macro_rules! check {
            ($opt:expr) => {
                if $opt.is_none() {
                    return -1;
                }
            };
        }
        check!(dist_start[target.0][target.1]);
        for d in &dist_traps {
            check!(d[start.0][start.1]);
        }

        // 起点到每个机关（必须经过一块石头）的最短距离
        let mut start_to_trap = Vec::with_capacity(t);
        for i in 0..t {
            if let Some(d) = Self::min_via_stone(&stones, &dist_start, &dist_traps[i]) {
                start_to_trap.push(d);
            } else {
                return -1;
            }
        }

        // 机关两两之间（经过一块石头）的最短距离
        let mut between = vec![vec![0u32; t]; t];
        for i in 0..t {
            for j in i + 1..t {
                if let Some(d) = Self::min_via_stone(&stones, &dist_traps[i], &dist_traps[j]) {
                    between[i][j] = d;
                    between[j][i] = d;
                } else {
                    return -1;
                }
            }
        }

        // 状态压缩 DP：dp[mask][last] 表示已访问机关集合 mask，最后在 last 的最小总距离
        let full = (1 << t) - 1;
        let mut dp = vec![vec![u32::MAX; t]; 1 << t];
        for i in 0..t {
            dp[1 << i][i] = start_to_trap[i];
        }

        for mask in 1..full {
            for last in 0..t {
                if dp[mask][last] == u32::MAX {
                    continue;
                }
                for nxt in 0..t {
                    if (mask >> nxt) & 1 == 0 {
                        let new_mask = mask | (1 << nxt);
                        let cost = dp[mask][last] + between[last][nxt];
                        if cost < dp[new_mask][nxt] {
                            dp[new_mask][nxt] = cost;
                        }
                    }
                }
            }
        }

        // 最后加上从最后一个机关到终点的距离（无需经过石头）
        let ans = (0..t)
            .filter_map(|last| {
                let d = dp[full][last];
                if d == u32::MAX {
                    None
                } else {
                    let to_target = dist_target[traps[last].0][traps[last].1]?;
                    Some(d + to_target)
                }
            })
            .min();
        ans.map(|x| x as i32).unwrap_or(-1)
    }
}
```
