---
title: "leetcode-矩阵14"
date: 2026-09-19T10:41:32+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 有效的井字游戏

给你一个字符串数组 board 表示井字游戏的棋盘。当且仅当在井字游戏过程中，棋盘有可能达到 board 所显示的状态时，才返回 true 。

井字游戏的棋盘是一个 3 x 3 数组，由字符 ' '，'X' 和 'O' 组成。字符 ' ' 代表一个空位。

以下是井字游戏的规则：

玩家轮流将字符放入空位（' '）中。
玩家 1 总是放字符 'X' ，而玩家 2 总是放字符 'O' 。
'X' 和 'O' 只允许放置在空位中，不允许对已放有字符的位置进行填充。
当有 3 个相同（且非空）的字符填充任何行、列或对角线时，游戏结束。
当所有位置非空时，也算为游戏结束。
如果游戏结束，玩家不允许再放置字符。


```
impl Solution {
    pub fn valid_tic_tac_toe(board: Vec<String>) -> bool {
        // 统计 X 和 O 的数量
        let (mut x_count, mut o_count) = (0, 0);
        for row in &board {
            for c in row.chars() {
                match c {
                    'X' => x_count += 1,
                    'O' => o_count += 1,
                    _ => {}
                }
            }
        }

        // 判断某个玩家是否获胜
        let win = |p: char| -> bool {
            let b: Vec<Vec<char>> = board.iter().map(|s| s.chars().collect()).collect();
            // 检查行和列
            for i in 0..3 {
                if (b[i][0] == p && b[i][1] == p && b[i][2] == p)
                    || (b[0][i] == p && b[1][i] == p && b[2][i] == p)
                {
                    return true;
                }
            }
            // 检查两条对角线
            (b[0][0] == p && b[1][1] == p && b[2][2] == p)
                || (b[0][2] == p && b[1][1] == p && b[2][0] == p)
        };

        // 合法性条件：
        // 1. X 数量等于 O 数量，或 X 比 O 多 1
        // 2. 若 X 获胜，则 X 数量必须比 O 多 1（X 刚下完）
        // 3. 若 O 获胜，则 X 数量必须等于 O 数量（O 刚下完）
        let count_valid = o_count == x_count || o_count == x_count - 1;
        let x_win_valid = !win('X') || o_count == x_count - 1;
        let o_win_valid = !win('O') || o_count == x_count;

        count_valid && x_win_valid && o_win_valid
    }
}
```
