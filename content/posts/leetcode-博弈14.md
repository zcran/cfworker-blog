---
title: "leetcode-博弈14"
date: 2026-04-09T20:51:31+08:00
tags: ["leetcode", "博弈"]
draft: false
---


## 猫和老鼠 II

一只猫和一只老鼠在玩一个叫做猫和老鼠的游戏。

它们所处的环境设定是一个 rows x cols 的方格 grid ，其中每个格子可能是一堵墙、一块地板、一位玩家（猫或者老鼠）或者食物。

玩家由字符 'C' （代表猫）和 'M' （代表老鼠）表示。
地板由字符 '.' 表示，玩家可以通过这个格子。
墙用字符 '#' 表示，玩家不能通过这个格子。
食物用字符 'F' 表示，玩家可以通过这个格子。
字符 'C' ， 'M' 和 'F' 在 grid 中都只会出现一次。
猫和老鼠按照如下规则移动：

老鼠 先移动 ，然后两名玩家轮流移动。
每一次操作时，猫和老鼠可以跳到上下左右四个方向之一的格子，他们不能跳过墙也不能跳出 grid 。
catJump 和 mouseJump 是猫和老鼠分别跳一次能到达的最远距离，它们也可以跳小于最大距离的长度。
它们可以停留在原地。
老鼠可以跳跃过猫的位置。
游戏有 4 种方式会结束：

如果猫跟老鼠处在相同的位置，那么猫获胜。
如果猫先到达食物，那么猫获胜。
如果老鼠先到达食物，那么老鼠获胜。
如果老鼠不能在 1000 次操作以内到达食物，那么猫获胜。
给你 rows x cols 的矩阵 grid 和两个整数 catJump 和 mouseJump ，双方都采取最优策略，如果老鼠获胜，那么请你返回 true ，否则返回 false 。


```
// 题目：猫和老鼠 II（LeetCode 1728）
// 算法：逆向 BFS + 拓扑排序（胜/负/平局判定）
// 状态：(mouse_pos, cat_pos, turn)，turn = 0 老鼠回合，1 猫回合
// 胜负规则：
//   - 老鼠先到食物则老鼠赢
//   - 猫先到食物或与老鼠在同一格则猫赢
//   - 双方轮流移动，可停留在原地或跳跃 1..=jump 步（不能穿墙）
// 解法：从已知结果反向传播，使用 degree 计数
use std::collections::VecDeque;

impl Solution {
    pub fn can_mouse_win(grid: Vec<String>, cat_jump: i32, mouse_jump: i32) -> bool {
        let grid: Vec<Vec<char>> = grid.into_iter()
            .map(|s| s.chars().collect())
            .collect();
        let (m, n) = (grid.len(), grid[0].len());
        let total = m * n;

        // 预处理每个位置可以移动到的所有位置（包括停留）
        let build_graph = |jump: i32| -> Vec<Vec<usize>> {
            let mut graph = vec![vec![]; total];
            for i in 0..m {
                for j in 0..n {
                    let idx = i * n + j;
                    if grid[i][j] == '#' { continue; }
                    // 停留
                    graph[idx].push(idx);
                    // 四个方向
                    for (dx, dy) in &[(-1, 0), (1, 0), (0, -1), (0, 1)] {
                        for k in 1..=jump {
                            let ni = i as i32 + dx * k;
                            let nj = j as i32 + dy * k;
                            if ni < 0 || ni >= m as i32 || nj < 0 || nj >= n as i32 {
                                break;
                            }
                            let ni = ni as usize;
                            let nj = nj as usize;
                            if grid[ni][nj] == '#' { break; }
                            graph[idx].push(ni * n + nj);
                        }
                    }
                }
            }
            graph
        };

        let mouse_graph = build_graph(mouse_jump);
        let cat_graph = build_graph(cat_jump);

        // 定位初始位置和食物
        let (mut mouse_start, mut cat_start, mut food) = (0, 0, 0);
        for i in 0..m {
            for j in 0..n {
                let idx = i * n + j;
                match grid[i][j] {
                    'M' => mouse_start = idx,
                    'C' => cat_start = idx,
                    'F' => food = idx,
                    _ => {}
                }
            }
        }

        // 状态总数：老鼠位置 * 猫位置 * 2 种回合
        let states = total * total * 2;
        // 结果数组：-1 未知，0 猫赢，1 老鼠赢
        let mut res = vec![vec![vec![-1; 2]; total]; total];
        // 度数：每个状态的出边数量（用于逆向BFS）
        let mut degree = vec![vec![vec![0; 2]; total]; total];

        // 初始化度数：统计每个状态的下一个状态数量
        for mpos in 0..total {
            if grid[mpos / n][mpos % n] == '#' { continue; }
            for cpos in 0..total {
                if grid[cpos / n][cpos % n] == '#' { continue; }
                for turn in 0..2 {
                    if turn == 0 {  // 老鼠回合
                        degree[mpos][cpos][turn] = mouse_graph[mpos].len();
                    } else {        // 猫回合
                        degree[mpos][cpos][turn] = cat_graph[cpos].len();
                    }
                }
            }
        }

        // 队列用于 BFS
        let mut queue = VecDeque::new();

        // 基础状态：猫和老鼠在同一位置 -> 猫赢（老鼠被捉）
        for pos in 0..total {
            if grid[pos / n][pos % n] == '#' { continue; }
            for turn in 0..2 {
                res[pos][pos][turn] = 0; // 猫赢
                queue.push_back((pos, pos, turn));
            }
        }
        // 基础状态：猫在食物位置 -> 猫赢
        for mpos in 0..total {
            if grid[mpos / n][mpos % n] == '#' { continue; }
            for turn in 0..2 {
                if res[mpos][food][turn] == -1 {
                    res[mpos][food][turn] = 0;
                    queue.push_back((mpos, food, turn));
                }
            }
        }
        // 基础状态：老鼠在食物位置且猫不在食物 -> 老鼠赢
        for cpos in 0..total {
            if grid[cpos / n][cpos % n] == '#' { continue; }
            for turn in 0..2 {
                if cpos != food && res[food][cpos][turn] == -1 {
                    res[food][cpos][turn] = 1;
                    queue.push_back((food, cpos, turn));
                }
            }
        }

        // 逆向 BFS
        while let Some((mpos, cpos, turn)) = queue.pop_front() {
            let winner = res[mpos][cpos][turn];
            // 根据当前状态的胜者，逆向推出前驱状态的结果
            if turn == 0 { // 当前是老鼠回合，前驱是猫回合
                // 前驱状态：(mpos, prev_cpos, 1)，猫从 prev_cpos 走到 cpos
                for &prev_cpos in &cat_graph[cpos] {
                    if res[mpos][prev_cpos][1] != -1 { continue; }
                    if winner == 0 { // 当前猫赢，意味着前驱猫能导致猫赢
                        res[mpos][prev_cpos][1] = 0;
                        queue.push_back((mpos, prev_cpos, 1));
                    } else { // 当前老鼠赢，前驱猫需要看是否所有选择都导致老鼠赢
                        degree[mpos][prev_cpos][1] -= 1;
                        if degree[mpos][prev_cpos][1] == 0 {
                            res[mpos][prev_cpos][1] = 1;
                            queue.push_back((mpos, prev_cpos, 1));
                        }
                    }
                }
            } else { // 当前是猫回合，前驱是老鼠回合
                for &prev_mpos in &mouse_graph[mpos] {
                    if res[prev_mpos][cpos][0] != -1 { continue; }
                    if winner == 1 { // 当前老鼠赢，前驱老鼠能导致老鼠赢
                        res[prev_mpos][cpos][0] = 1;
                        queue.push_back((prev_mpos, cpos, 0));
                    } else {
                        degree[prev_mpos][cpos][0] -= 1;
                        if degree[prev_mpos][cpos][0] == 0 {
                            res[prev_mpos][cpos][0] = 0;
                            queue.push_back((prev_mpos, cpos, 0));
                        }
                    }
                }
            }
        }

        // 最终答案：初始状态（老鼠回合）的结果是否为老鼠赢
        res[mouse_start][cat_start][0] == 1
    }
}
```
