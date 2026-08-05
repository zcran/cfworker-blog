---
title: "leetcode-回溯56"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 棋盘上有效移动组合的数目

有一个 8 x 8 的棋盘，它包含 n 个棋子（棋子包括车，后和象三种）。给你一个长度为 n 的字符串数组 pieces ，其中 pieces[i] 表示第 i 个棋子的类型（车，后或象）。除此以外，还给你一个长度为 n 的二维整数数组 positions ，其中 positions[i] = [ri, ci] 表示第 i 个棋子现在在棋盘上的位置为 (ri, ci) ，棋盘下标从 1 开始。

每个棋子的移动中，首先选择移动的 方向 ，然后选择 移动的步数 ，同时你要确保移动过程中棋子不能移到棋盘以外的地方。棋子需按照以下规则移动：

· 车可以 水平或者竖直 从 (r, c) 沿着方向 (r+1, c)，(r-1, c)，(r, c+1) 或者 (r, c-1) 移动。

· 后可以 水平竖直或者斜对角 从 (r, c) 沿着方向 (r+1, c)，(r-1, c)，(r, c+1)，(r, c-1)，(r+1, c+1)，(r+1, c-1)，(r-1, c+1)，(r-1, c-1) 移动。

· 象可以 斜对角 从 (r, c) 沿着方向 (r+1, c+1)，(r+1, c-1)，(r-1, c+1)，(r-1, c-1) 移动。

你必须同时 移动 棋盘上的每一个棋子。移动组合 包含所有棋子的 移动 。每一秒，每个棋子都沿着它们选择的方向往前移动 一步 ，直到它们到达目标位置。所有棋子从时刻 0 开始移动。如果在某个时刻，两个或者更多棋子占据了同一个格子，那么这个移动组合 不有效 。

请你返回 有效 移动组合的数目。

注意：

· 初始时，不会有两个棋子 在 同一个位置 。
· 有可能在一个移动组合中，有棋子不移动。
· 如果两个棋子 直接相邻 且两个棋子下一秒要互相占据对方的位置，可以将它们在同一秒内 交换位置 。


```
use std::collections::HashSet;

impl Solution {
    pub fn count_combinations(pieces: Vec<String>, positions: Vec<Vec<i32>>) -> i32 {
        // 定义各类棋子的移动方向
        const ROOK_DIRS: [(i32, i32); 4] = [(1, 0), (-1, 0), (0, 1), (0, -1)];
        const BISHOP_DIRS: [(i32, i32); 4] = [(1, 1), (1, -1), (-1, 1), (-1, -1)];
        const QUEEN_DIRS: [(i32, i32); 8] = [
            (1, 0), (-1, 0), (0, 1), (0, -1),
            (1, 1), (1, -1), (-1, 1), (-1, -1)
        ];

        let n = pieces.len();
        let mut result = 0;
        let mut movements = Vec::new();

        // 获取棋子对应的方向数组
        fn get_directions(piece: &str) -> &'static [(i32, i32)] {
            match piece {
                "rook" => &ROOK_DIRS,
                "queen" => &QUEEN_DIRS,
                _ => &BISHOP_DIRS, // "bishop"
            }
        }

        // 检查两个移动路径是否相交（包括同时到达同一格）
        fn is_conflict(m1: &mut Movement, m2: &mut Movement) -> bool {
            // 重置到起始位置
            m1.cur_x = m1.start_x;
            m1.cur_y = m1.start_y;
            m2.cur_x = m2.start_x;
            m2.cur_y = m2.start_y;

            // 模拟移动，直到两个棋子都停止
            while !m1.stopped() || !m2.stopped() {
                m1.advance();
                m2.advance();
                if m1.cur_x == m2.cur_x && m1.cur_y == m2.cur_y {
                    return true; // 发生碰撞
                }
            }
            false
        }

        // 检查新棋子是否与所有已放置棋子冲突
        fn check_conflicts(movements: &mut Vec<Movement>, idx: usize) -> bool {
            for i in 0..idx {
                // 使用 split_at_mut 避免借用冲突
                let (left, right) = movements.split_at_mut(idx);
                if is_conflict(&mut left[i], &mut right[0]) {
                    return false;
                }
            }
            true
        }

        // 深度优先搜索枚举所有移动组合
        fn dfs(
            pieces: &[String],
            positions: &[Vec<i32>],
            idx: usize,
            movements: &mut Vec<Movement>,
            result: &mut i32,
        ) {
            if idx == pieces.len() {
                *result += 1;
                return;
            }

            let (r, c) = (positions[idx][0], positions[idx][1]);
            let dirs = get_directions(&pieces[idx]);

            // 情况1：棋子不移动
            movements.push(Movement::new(r, c, r, c, 0, 0));
            if check_conflicts(movements, idx) {
                dfs(pieces, positions, idx + 1, movements, result);
            }
            movements.pop();

            // 情况2：棋子沿某个方向移动1-7步
            for &(dr, dc) in dirs {
                for step in 1..8 {
                    let nr = r + dr * step;
                    let nc = c + dc * step;
                    if nr < 1 || nr > 8 || nc < 1 || nc > 8 {
                        break; // 超出棋盘，后面的步数更大也会超出
                    }

                    movements.push(Movement::new(r, c, nr, nc, dr, dc));
                    if check_conflicts(movements, idx) {
                        dfs(pieces, positions, idx + 1, movements, result);
                    }
                    movements.pop();
                }
            }
        }

        dfs(&pieces, &positions, 0, &mut movements, &mut result);
        result
    }
}

/// 表示一个棋子的移动状态
#[derive(Clone)]
struct Movement {
    start_x: i32,
    start_y: i32,
    end_x: i32,
    end_y: i32,
    dx: i32,
    dy: i32,
    cur_x: i32,
    cur_y: i32,
}

impl Movement {
    fn new(start_x: i32, start_y: i32, end_x: i32, end_y: i32, dx: i32, dy: i32) -> Self {
        Movement {
            start_x,
            start_y,
            end_x,
            end_y,
            dx,
            dy,
            cur_x: start_x,
            cur_y: start_y,
        }
    }

    /// 判断棋子是否已到达目标位置
    fn stopped(&self) -> bool {
        self.cur_x == self.end_x && self.cur_y == self.end_y
    }

    /// 向前移动一步
    fn advance(&mut self) {
        if !self.stopped() {
            self.cur_x += self.dx;
            self.cur_y += self.dy;
        }
    }
}
```
