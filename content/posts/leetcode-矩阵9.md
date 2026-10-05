---
title: "leetcode-矩阵9"
date: 2026-09-19T10:41:32+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 棋盘上的战舰

给你一个大小为 m x n 的矩阵 board 表示棋盘，其中，每个单元格可以是一艘战舰 'X' 或者是一个空位 '.' ，返回在棋盘 board 上放置的 舰队 的数量。

舰队 只能水平或者垂直放置在 board 上。换句话说，舰队只能按 1 x k（1 行，k 列）或 k x 1（k 行，1 列）的形状放置，其中 k 可以是任意大小。两个舰队之间至少有一个水平或垂直的空格分隔 （即没有相邻的舰队）。



```
impl Solution {
    pub fn count_battleships(board: Vec<Vec<char>>) -> i32 {
        let rows = board.len();
        let cols = board[0].len();
        let mut ans = 0;

        // 关键观察：一艘战舰只需在其"起点"被计数一次。
        // 起点定义为其左侧和上方都不是 'X' 的 'X' 格子。
        // 这样无需修改矩阵，也无需遍历整艘船，一次扫描即可。
        for i in 0..rows {
            for j in 0..cols {
                if board[i][j] == 'X'
                    && (i == 0 || board[i - 1][j] != 'X')   // 上方不是 'X'
                    && (j == 0 || board[i][j - 1] != 'X')   // 左侧不是 'X'
                {
                    ans += 1;
                }
            }
        }

        ans
    }
}
```
