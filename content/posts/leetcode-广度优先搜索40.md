---
title: "leetcode-广度优先搜索40"
date: 2026-09-13T09:32:08+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 逃离火灾

给你一个下标从 0 开始大小为 m x n 的二维整数数组 grid ，它表示一个网格图。每个格子为下面 3 个值之一：

0 表示草地。
1 表示着火的格子。
2 表示一座墙，你跟火都不能通过这个格子。

一开始你在最左上角的格子 (0, 0) ，你想要到达最右下角的安全屋格子 (m - 1, n - 1) 。每一分钟，你可以移动到 相邻 的草地格子。每次你移动 之后 ，着火的格子会扩散到所有不是墙的 相邻 格子。

请你返回你在初始位置可以停留的 最多 分钟数，且停留完这段时间后你还能安全到达安全屋。如果无法实现，请你返回 -1 。如果不管你在初始位置停留多久，你 总是 能到达安全屋，请你返回 109 。

注意，如果你到达安全屋后，火马上到了安全屋，这视为你能够安全到达安全屋。

如果两个格子有共同边，那么它们为 相邻 格子。


```
impl Solution {
    pub fn maximum_minutes(grid: Vec<Vec<i32>>) -> i32 {
        let (m, n) = (grid.len(), grid[0].len());
        const INF: i32 = 1_000_000_000;

        // 检查：在起点停留 t 分钟后，能否安全到达右下角
        let check = |t: i32| -> bool {
            let mut on_fire = vec![vec![false; n]; m];
            let mut fire = Vec::new();
            for i in 0..m {
                for j in 0..n {
                    if grid[i][j] == 1 {
                        on_fire[i][j] = true;
                        fire.push((i, j));
                    }
                }
            }

            // 让火先蔓延 t 分钟（模拟停留时间）
            let mut remaining = t;
            while remaining > 0 && !fire.is_empty() {
                let mut next = Vec::new();
                for &(i, j) in &fire {
                    for (x, y) in neighbors(i, j, m, n) {
                        if !on_fire[x][y] && grid[x][y] == 0 {
                            on_fire[x][y] = true;
                            next.push((x, y));
                        }
                    }
                }
                fire = next;
                remaining -= 1;
            }

            // 起点已着火则失败
            if on_fire[0][0] {
                return false;
            }

            // BFS 模拟人移动，人每走一步火也蔓延一步
            let mut visited = vec![vec![false; n]; m];
            visited[0][0] = true;
            let mut queue = vec![(0usize, 0usize)];

            while !queue.is_empty() {
                let mut next_people = Vec::new();
                for &(i, j) in &queue {
                    if on_fire[i][j] {
                        continue; // 人到达时该格已着火
                    }
                    for (x, y) in neighbors(i, j, m, n) {
                        if on_fire[x][y] || visited[x][y] || grid[x][y] != 0 {
                            continue;
                        }
                        // 到达安全屋即成功（即使火下一步就到）
                        if x == m - 1 && y == n - 1 {
                            return true;
                        }
                        visited[x][y] = true;
                        next_people.push((x, y));
                    }
                }

                // 人移动后火蔓延一步
                let mut next_fire = Vec::new();
                for &(i, j) in &fire {
                    for (x, y) in neighbors(i, j, m, n) {
                        if !on_fire[x][y] && grid[x][y] == 0 {
                            on_fire[x][y] = true;
                            next_fire.push((x, y));
                        }
                    }
                }

                queue = next_people;
                fire = next_fire;
            }

            false
        };

        // 二分最大停留时间
        let mut left = -1;
        let mut right = (m * n) as i32 + 1;
        while left + 1 < right {
            let mid = (left + right) / 2;
            if check(mid) {
                left = mid;
            } else {
                right = mid;
            }
        }

        if left < (m * n) as i32 {
            left
        } else {
            INF
        }
    }
}

/// 返回 (i, j) 的上下左右四个相邻坐标（带边界检查）
fn neighbors(i: usize, j: usize, m: usize, n: usize) -> Vec<(usize, usize)> {
    let mut res = Vec::with_capacity(4);
    if i > 0 { res.push((i - 1, j)); }
    if i + 1 < m { res.push((i + 1, j)); }
    if j > 0 { res.push((i, j - 1)); }
    if j + 1 < n { res.push((i, j + 1)); }
    res
}
```
