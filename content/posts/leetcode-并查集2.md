---
title: "leetcode-并查集2"
date: 2026-06-25T11:22:21+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 被围绕的区域

给你一个 m x n 的矩阵 board ，由若干字符 'X' 和 'O' 组成，捕获 所有 被围绕的区域：

· 连接：一个单元格与水平或垂直方向上相邻的单元格连接。
· 区域：连接所有 'O' 的单元格来形成一个区域。
· 围绕：如果一个区域中的所有 'O' 单元格都不在棋盘的边缘，则该区域被包围。这样的区域 完全 被 'X' 单元格包围。

通过 原地 将输入矩阵中的所有 'O' 替换为 'X' 来 捕获被围绕的区域。你不需要返回任何值。


```
impl Solution {
    pub fn solve(board: &mut Vec<Vec<char>>) {
        let (m, n) = (board.len(), board[0].len());

        // 从边缘 'O' 开始 DFS，标记为 'T'（临时标记）
        let mut stack = Vec::new();

        // 边界初始化：第一列和最后一列
        for i in 0..m {
            if board[i][0] == 'O' { Self::mark(&mut stack, board, i, 0); }
            if n > 1 && board[i][n-1] == 'O' { Self::mark(&mut stack, board, i, n-1); }
        }
        // 边界初始化：第一行和最后一行（跳过角落避免重复）
        for j in 1..n-1 {
            if board[0][j] == 'O' { Self::mark(&mut stack, board, 0, j); }
            if m > 1 && board[m-1][j] == 'O' { Self::mark(&mut stack, board, m-1, j); }
        }

        // DFS 遍历所有与边缘相连的 'O'
        while let Some((x, y)) = stack.pop() {
            for (dx, dy) in [(0, 1), (0, -1), (1, 0), (-1, 0)] {
                let (nx, ny) = (x as isize + dx, y as isize + dy);
                if nx >= 0 && nx < m as isize && ny >= 0 && ny < n as isize {
                    let (nx, ny) = (nx as usize, ny as usize);
                    if board[nx][ny] == 'O' {
                        Self::mark(&mut stack, board, nx, ny);
                    }
                }
            }
        }

        // 最终转换：'O' → 'X'（被包围），'T' → 'O'（边缘连通）
        for row in board {
            for cell in row {
                *cell = if *cell == 'T' { 'O' } else { 'X' };
            }
        }
    }

    #[inline]
    fn mark(stack: &mut Vec<(usize, usize)>, board: &mut Vec<Vec<char>>, x: usize, y: usize) {
        board[x][y] = 'T';
        stack.push((x, y));
    }
}
```
