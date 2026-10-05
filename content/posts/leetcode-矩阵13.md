---
title: "leetcode-矩阵13"
date: 2026-09-19T10:41:32+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 变为棋盘

一个 n x n 的二维网络 board 仅由 0 和 1 组成 。每次移动，你能交换任意两列或是两行的位置。

返回 将这个矩阵变为  “棋盘”  所需的最小移动次数 。如果不存在可行的变换，输出 -1。

“棋盘” 是指任意一格的上下左右四个方向的值均与本身不同的矩阵。


```
impl Solution {
    pub fn moves_to_chessboard(board: Vec<Vec<i32>>) -> i32 {
        let n = board.len();

        // 用位掩码表示一行/一列的模式，方便快速比较与统计。
        // 棋盘合法时，每一行只能是第一行的原样或完全取反，列同理。
        let mut row_mask = 0usize;
        let mut col_mask = 0usize;
        for i in 0..n {
            row_mask |= (board[0][i] as usize) << i;
            col_mask |= (board[i][0] as usize) << i;
        }

        let full_mask = (1usize << n) - 1;
        let rev_row_mask = full_mask ^ row_mask;
        let rev_col_mask = full_mask ^ col_mask;

        let mut row_same = 0; // 与第一行完全相同的行数
        let mut col_same = 0; // 与第一列完全相同的列数

        for i in 0..n {
            let mut cur_row = 0usize;
            let mut cur_col = 0usize;
            for j in 0..n {
                cur_row |= (board[i][j] as usize) << j;
                cur_col |= (board[j][i] as usize) << j;
            }

            // 每一行必须是 row_mask 或其取反；每一列同理
            if (cur_row != row_mask && cur_row != rev_row_mask)
                || (cur_col != col_mask && cur_col != rev_col_mask)
            {
                return -1;
            }

            if cur_row == row_mask {
                row_same += 1;
            }
            if cur_col == col_mask {
                col_same += 1;
            }
        }

        // 计算把 mask 调整为交替模式所需的最小交换次数（按行或按列）。
        // count 表示与基准 mask 相同的行/列数目。
        fn min_moves(mask: usize, count: i32, n: usize) -> i32 {
            let ones = mask.count_ones() as i32;
            let even = (mask & 0xAAAA_AAAA).count_ones() as i32; // 偶数位上 1 的个数
            let odd = (mask & 0x5555_5555).count_ones() as i32;  // 奇数位上 1 的个数

            if n % 2 == 1 {
                // 奇数长度：1 与 0 的数目相差 1
                if (n as i32 - 2 * ones).abs() != 1 || (n as i32 - 2 * count).abs() != 1 {
                    return -1;
                }
                if ones == (n / 2) as i32 {
                    // 需要把 1 放在偶数位
                    (n as i32 / 2) - even
                } else {
                    // 需要把 1 放在奇数位
                    ((n as i32 + 1) / 2) - odd
                }
            } else {
                // 偶数长度：1 与 0 的数目相等
                if ones != (n / 2) as i32 || count != (n / 2) as i32 {
                    return -1;
                }
                // 两种交替模式取较小交换次数
                let moves_even = (n as i32 / 2) - even;
                let moves_odd = (n as i32 / 2) - odd;
                moves_even.min(moves_odd)
            }
        }

        let row_moves = min_moves(row_mask, row_same, n);
        let col_moves = min_moves(col_mask, col_same, n);
        if row_moves == -1 || col_moves == -1 {
            -1
        } else {
            row_moves + col_moves
        }
    }
}
```
