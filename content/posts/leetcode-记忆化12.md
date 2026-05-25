---
title: "leetcode-记忆化12"
date: 2026-05-06T20:22:33+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 滑动谜题

在一个 2 x 3 的板上（board）有 5 块砖瓦，用数字 1~5 来表示, 以及一块空缺用 0 来表示。一次 移动 定义为选择 0 与一个相邻的数字（上下左右）进行交换.

最终当板 board 的结果是 [[1,2,3],[4,5,0]] 谜板被解开。

给出一个谜板的初始状态 board ，返回最少可以通过多少次移动解开谜板，如果不能解开谜板，则返回 -1 。

```
use std::collections::HashSet;

impl Solution {
    /// 2x3 滑动谜题（类似华容道）的最小移动步数。
    /// 状态编码为一个 6 位十进制整数，例如 [[1,2,3],[4,5,0]] 编码为 123450。
    /// 移动规则：每次将相邻的数字与 0 交换。
    pub fn sliding_puzzle(board: Vec<Vec<i32>>) -> i32 {
        // 允许的移动方向
        enum Direction { Up, Down, Left, Right }

        // ---------- 辅助函数：状态解码与编码 ----------
        /// 将棋盘展平为 6 位整数（首位对应 board[0][0]）
        fn encode(board: &[Vec<i32>]) -> usize {
            board.iter()
                .flat_map(|row| row.iter())
                .enumerate()
                .fold(0, |state, (idx, &digit)| {
                    // 位置 idx (0~5) 对应的十进制位权为 10^(5-idx)
                    let weight = 10_usize.pow((5 - idx) as u32);
                    state + (digit as usize) * weight
                })
        }

        /// 从状态中提取第 row 行、第 col 列的数字 (row, col ∈ {0,1}×{0,1,2})
        fn digit_at(state: usize, row: usize, col: usize) -> usize {
            // 6 位数字：高位为 (0,0) → 低位为 (1,2)
            let pos = row * 3 + col;          // 0~5 线性索引
            let weight = 10_usize.pow((5 - pos) as u32);
            (state / weight) % 10
        }

        /// 设置状态中指定位置 digit，返回新状态。
        fn with_digit(state: usize, row: usize, col: usize, digit: usize) -> usize {
            let pos = row * 3 + col;
            let weight = 10_usize.pow((5 - pos) as u32);
            let old_digit = (state / weight) % 10;
            state - old_digit * weight + digit * weight
        }

        /// 找到空格（数字 0）的行列坐标。
        fn zero_position(state: usize) -> (usize, usize) {
            for pos in 0..6 {
                let weight = 10_usize.pow((5 - pos) as u32);
                if (state / weight) % 10 == 0 {
                    return (pos / 3, pos % 3);
                }
            }
            unreachable!("状态中必须包含 0")
        }

        // ---------- 核心移动逻辑 ----------
        /// 尝试向某一方向移动空格，返回新状态；若移动非法则返回 None。
        fn try_move(state: usize, dir: &Direction) -> Option<usize> {
            let (row, col) = zero_position(state);
            // 根据方向计算目标位置
            let (new_row, new_col) = match dir {
                Direction::Up    => (row.checked_sub(1)?, col),
                Direction::Down  => (row + 1, col),
                Direction::Left  => (row, col.checked_sub(1)?),
                Direction::Right => (row, col + 1),
            };
            // 检查是否越界（2行3列）
            if new_row > 1 || new_col > 2 {
                return None;
            }
            // 交换空格与相邻数字
            let digit = digit_at(state, new_row, new_col);
            let state = with_digit(state, row, col, digit);   // 原空格位置填入 digit
            let state = with_digit(state, new_row, new_col, 0); // 新位置变成 0
            Some(state)
        }

        // ---------- BFS 主逻辑 ----------
        let start = encode(&board);
        let target = 123450_usize;
        if start == target {
            return 0;
        }

        let directions = [Direction::Up, Direction::Down, Direction::Left, Direction::Right];
        let mut visited = HashSet::new();
        let mut current_level = vec![start];
        visited.insert(start);
        let mut steps = 0;

        while !current_level.is_empty() {
            steps += 1;
            // 函数式生成下一层：对当前层的每个状态尝试所有方向，过滤非法且未访问的状态
            let next_level: Vec<usize> = current_level.iter()
                .flat_map(|&state| {
                    directions.iter().filter_map(move |dir| try_move(state, dir))
                })
                .filter(|&next| !visited.contains(&next))
                .collect();

            // 检查目标是否已出现
            if next_level.iter().any(|&s| s == target) {
                return steps;
            }

            visited.extend(next_level.iter());
            current_level = next_level;
        }

        -1 // 无解
    }
}
```
