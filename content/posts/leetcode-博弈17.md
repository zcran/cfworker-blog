---
title: "leetcode-博弈17"
date: 2026-04-09T20:51:31+08:00
tags: ["leetcode", "博弈"]
draft: false
---


## 吃掉所有兵需要的最多移动次数

给你一个 50 x 50 的国际象棋棋盘，棋盘上有 一个 马和一些兵。给你两个整数 kx 和 ky ，其中 (kx, ky) 表示马所在的位置，同时还有一个二维数组 positions ，其中 positions[i] = [xi, yi] 表示第 i 个兵在棋盘上的位置。

Alice 和 Bob 玩一个回合制游戏，Alice 先手。玩家的一次操作中，可以执行以下操作：

玩家选择一个仍然在棋盘上的兵，然后移动马，通过 最少 的 步数 吃掉这个兵。注意 ，玩家可以选择 任意 一个兵，不一定 要选择从马的位置出发 最少 移动步数的兵。
在马吃兵的过程中，马 可能 会经过一些其他兵的位置，但这些兵 不会 被吃掉。只有 选中的兵在这个回合中被吃掉。
Alice 的目标是 最大化 两名玩家的 总 移动次数，直到棋盘上不再存在兵，而 Bob 的目标是 最小化 总移动次数。

假设两名玩家都采用 最优 策略，请你返回可以达到的 最大 总移动次数。

在一次 移动 中，如下图所示，马有 8 个可以移动到的位置，每个移动位置都是沿着坐标轴的一个方向前进 2 格，然后沿着垂直的方向前进 1 格。


```
// 题目：骑士博弈问题（LeetCode 2056 类似变种）
// 题意：马从起点 (kx, ky) 出发，要去吃掉所有兵（positions 中的点）。
//       Alice 和 Bob 轮流移动马，Alice 先手。
//       Alice 想最大化总移动步数，Bob 想最小化总步数。双方都最优。
//      求最终的总步数。
//
// 解法：
//  1. 预处理每个兵到棋盘上所有格子的最短步数（BFS，棋盘 50x50）。
//  2. 状态压缩 DP：mask 表示尚未被吃的兵集合。
//     dp[i][mask] 表示当前马在 positions[i]（i 可以是兵或起点），
//     且剩余未吃兵集合为 mask 时的最优总步数（从当前局面开始）。
//     轮到谁由已吃兵数量的奇偶性决定（Alice 先手，已吃偶数个时 Alice 走）。
//     转移：枚举下一个要吃的兵 j ∈ mask，代价为 dis[j][pos_i]，
//           下一状态为 (j, mask without j)，根据当前玩家选择 max 或 min。
//     最终答案：dp[n][full_mask]（n 为起点索引）。
use std::collections::VecDeque;

impl Solution {
    pub fn max_moves(kx: i32, ky: i32, positions: Vec<Vec<i32>>) -> i32 {
        const BOARD_SIZE: usize = 50;
        const DIRS: [(i32, i32); 8] = [
            (2, 1), (1, 2), (-1, 2), (-2, 1),
            (-2, -1), (-1, -2), (1, -2), (2, -1),
        ];

        let n = positions.len();                 // 兵的数量
        let mut positions = positions;           // 变为可变
        positions.push(vec![kx, ky]);            // 起点作为最后一个位置（索引 n）

        // ---------- 1. BFS 预处理每个兵到所有格子的最短步数 ----------
        // dis[p][x][y] 表示从第 p 个兵出发到 (x,y) 的最少马步数
        let mut dis = vec![vec![vec![-1; BOARD_SIZE]; BOARD_SIZE]; n];
        for (p, pos) in positions.iter().take(n).enumerate() {
            let (px, py) = (pos[0] as usize, pos[1] as usize);
            let mut queue = VecDeque::new();
            queue.push_back((px, py));
            dis[p][px][py] = 0;
            let mut step = 1;
            while !queue.is_empty() {
                let level_size = queue.len();
                for _ in 0..level_size {
                    let (x, y) = queue.pop_front().unwrap();
                    for &(dx, dy) in &DIRS {
                        let nx = x as i32 + dx;
                        let ny = y as i32 + dy;
                        if nx >= 0 && nx < BOARD_SIZE as i32 && ny >= 0 && ny < BOARD_SIZE as i32 {
                            let (nx, ny) = (nx as usize, ny as usize);
                            if dis[p][nx][ny] == -1 {
                                dis[p][nx][ny] = step;
                                queue.push_back((nx, ny));
                            }
                        }
                    }
                }
                step += 1;
            }
        }

        // ---------- 2. 状态压缩 DP（记忆化搜索） ----------
        let full_mask = (1 << n) - 1;                 // 所有兵都未吃
        // memo[i][mask]：当前马在 positions[i]，剩余未吃兵集合为 mask 时的最优总步数
        let mut memo = vec![vec![-1; 1 << n]; n + 1];

        // 递归函数，所有捕获变量均通过参数显式传递
        fn dfs(
            i: usize,
            mask: usize,
            n: usize,
            positions: &Vec<Vec<i32>>,
            dis: &Vec<Vec<Vec<i32>>>,
            memo: &mut Vec<Vec<i32>>,
            full_mask: usize,
        ) -> i32 {
            if mask == 0 {
                return 0;
            }
            if memo[i][mask] != -1 {
                return memo[i][mask];
            }

            let (x, y) = (positions[i][0] as usize, positions[i][1] as usize);
            let eaten = full_mask ^ mask;          // 已吃兵集合
            let alice_turn = eaten.count_ones() % 2 == 0; // 已吃偶数个 → Alice 回合

            if alice_turn {
                // Alice 想最大化总步数
                let mut best = 0;
                for j in 0..n {
                    if (mask >> j) & 1 == 1 {
                        let step_cost = dis[j][x][y];      // 从当前位置到兵 j 的步数
                        let next_val = dfs(j, mask ^ (1 << j), n, positions, dis, memo, full_mask);
                        best = best.max(step_cost + next_val);
                    }
                }
                memo[i][mask] = best;
            } else {
                // Bob 想最小化总步数
                let mut best = i32::MAX;
                for j in 0..n {
                    if (mask >> j) & 1 == 1 {
                        let step_cost = dis[j][x][y];
                        let next_val = dfs(j, mask ^ (1 << j), n, positions, dis, memo, full_mask);
                        best = best.min(step_cost + next_val);
                    }
                }
                memo[i][mask] = best;
            }
            memo[i][mask]
        }

        dfs(n, full_mask, n, &positions, &dis, &mut memo, full_mask)
    }
}
```
