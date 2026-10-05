---
title: "leetcode-广度优先搜索10"
date: 2026-09-13T09:32:06+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 扫雷游戏

让我们一起来玩扫雷游戏！

给你一个大小为 m x n 二维字符矩阵 board ，表示扫雷游戏的盘面，其中：

· 'M' 代表一个 未挖出的 地雷，
· 'E' 代表一个 未挖出的 空方块，
· 'B' 代表没有相邻（上，下，左，右，和所有4个对角线）地雷的 已挖出的 空白方块，
· 数字（'1' 到 '8'）表示有多少地雷与这块 已挖出的 方块相邻，
· 'X' 则表示一个 已挖出的 地雷。

给你一个整数数组 click ，其中 click = [clickr, clickc] 表示在所有 未挖出的 方块（'M' 或者 'E'）中的下一个点击位置（clickr 是行下标，clickc 是列下标）。

根据以下规则，返回相应位置被点击后对应的盘面：

1. 如果一个地雷（'M'）被挖出，游戏就结束了- 把它改为 'X' 。
2. 如果一个 没有相邻地雷 的空方块（'E'）被挖出，修改它为（'B'），并且所有和其相邻的 未挖出 方块都应该被递归地揭露。
3. 如果一个 至少与一个地雷相邻 的空方块（'E'）被挖出，修改它为数字（'1' 到 '8' ），表示相邻地雷的数量。
4. 如果在此次点击中，若无更多方块可被揭露，则返回盘面。


```
impl Solution {
    pub fn update_board(mut board: Vec<Vec<char>>, click: Vec<i32>) -> Vec<Vec<char>> {
        const DIRS: [(isize, isize); 8] = [
            (-1, -1), (-1, 0), (-1, 1),
            (0, -1),           (0, 1),
            (1, -1),  (1, 0),  (1, 1),
        ];

        let m = board.len();
        let n = board[0].len();
        let (r, c) = (click[0] as usize, click[1] as usize);

        // 点到地雷，直接标记为已挖出的地雷
        if board[r][c] == 'M' {
            board[r][c] = 'X';
            return board;
        }
        // 非空方块（数字或 'B'）无需处理
        if board[r][c] != 'E' {
            return board;
        }

        // 统计 (i, j) 周围的地雷数
        let count_mines = |i: usize, j: usize, board: &Vec<Vec<char>>| -> u8 {
            let mut cnt = 0;
            for &(di, dj) in &DIRS {
                let x = i as isize + di;
                let y = j as isize + dj;
                if x >= 0 && x < m as isize && y >= 0 && y < n as isize
                    && board[x as usize][y as usize] == 'M'
                {
                    cnt += 1;
                }
            }
            cnt
        };

        // BFS 揭露方块，用栈代替队列（顺序无关）
        let mut stack = vec![(r, c)];
        while let Some((i, j)) = stack.pop() {
            if board[i][j] != 'E' {
                continue;
            }
            let mines = count_mines(i, j, &board);
            if mines > 0 {
                // 周围有地雷，标记为数字，不再扩散
                board[i][j] = (b'0' + mines) as char;
            } else {
                // 周围无地雷，标记为 'B'，并将相邻未挖出方块加入栈
                board[i][j] = 'B';
                for &(di, dj) in &DIRS {
                    let x = i as isize + di;
                    let y = j as isize + dj;
                    if x >= 0 && x < m as isize && y >= 0 && y < n as isize {
                        let (x, y) = (x as usize, y as usize);
                        if board[x][y] == 'E' {
                            stack.push((x, y));
                        }
                    }
                }
            }
        }

        board
    }
}
```
