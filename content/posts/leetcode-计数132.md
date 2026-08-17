---
title: "leetcode-计数130"
date: 2026-08-01T10:40:58+08:00
tags: ["leetcode", "计数"]
draft: false
---


##  井字游戏

设计一个算法，判断玩家是否赢了井字游戏。输入是一个 N x N 的数组棋盘，由字符" "，"X"和"O"组成，其中字符" "代表一个空位。

以下是井字游戏的规则：

玩家轮流将字符放入空位（" "）中。
第一个玩家总是放字符"O"，且第二个玩家总是放字符"X"。
"X"和"O"只允许放置在空位中，不允许对已放有字符的位置进行填充。
当有N个相同（且非空）的字符填充任何行、列或对角线时，游戏结束，对应该字符的玩家获胜。
当所有位置非空时，也算为游戏结束。
如果游戏结束，玩家不允许再放置字符。

如果游戏存在获胜者，就返回该游戏的获胜者使用的字符（"X"或"O"）；如果游戏以平局结束，则返回 "Draw"；如果仍会有行动（游戏未结束），则返回 "Pending"。


```
impl Solution {
    /// 判断井字棋游戏结果。
    ///
    /// 检查是否有玩家在某行、某列或某对角线上连成 N 个。
    /// 若无获胜者且棋盘已满，返回 "Draw"；仍有空位则返回 "Pending"。
    pub fn tictactoe(board: Vec<String>) -> String {
        let n = board.len();
        if n == 0 {
            return "Draw".to_string();
        }

        // 辅助闭包：将获胜字节转为结果字符串
        let win = |c: u8| -> String {
            match c {
                b'X' => "X",
                b'O' => "O",
                _ => unreachable!(),
            }
            .to_string()
        };

        // 检查行，同时记录是否存在空位
        let mut has_empty = false;
        for i in 0..n {
            let row = board[i].as_bytes();
            // 检查空位
            if !has_empty && row.iter().any(|&c| c == b' ') {
                has_empty = true;
            }
            // 检查该行是否全为同一非空字符
            let first = row[0];
            if first != b' ' && row.iter().all(|&c| c == first) {
                return win(first);
            }
        }

        // 检查列
        for j in 0..n {
            let first = board[0].as_bytes()[j];
            if first != b' ' && (0..n).all(|i| board[i].as_bytes()[j] == first) {
                return win(first);
            }
        }

        // 检查主对角线
        let first = board[0].as_bytes()[0];
        if first != b' ' && (0..n).all(|i| board[i].as_bytes()[i] == first) {
            return win(first);
        }

        // 检查副对角线
        let first = board[0].as_bytes()[n - 1];
        if first != b' ' && (0..n).all(|i| board[i].as_bytes()[n - 1 - i] == first) {
            return win(first);
        }

        if has_empty {
            "Pending".to_string()
        } else {
            "Draw".to_string()
        }
    }
}
```
