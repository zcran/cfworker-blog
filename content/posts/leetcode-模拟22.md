---
title: "leetcode-模拟22"
date: 2026-08-08T11:31:09+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 可以被一步捕获的棋子数

给定一个 8 x 8 的棋盘，只有一个 白色的车，用字符 'R' 表示。棋盘上还可能存在白色的象 'B' 以及黑色的卒 'p'。空方块用字符 '.' 表示。

车可以按水平或竖直方向（上，下，左，右）移动任意个方格直到它遇到另一个棋子或棋盘的边界。如果它能够在一次移动中移动到棋子的方格，则能够 吃掉 棋子。

注意：车不能穿过其它棋子，比如象和卒。这意味着如果有其它棋子挡住了路径，车就不能够吃掉棋子。

返回白车 攻击 范围内 兵的数量。


```
impl Solution {
    /// 计算白车 'R' 在四个方向上能直接吃掉的黑卒 'p' 数量
    /// 车不能穿过象 'B'，遇到边界或象则停止该方向搜索
    pub fn num_rook_captures(board: Vec<Vec<char>>) -> i32 {
        // 方向向量：右、下、左、上
        const DIRS: [(i32, i32); 4] = [(0, 1), (1, 0), (0, -1), (-1, 0)];
        const SIZE: i32 = 8;

        // 定位白车位置
        let (mut r, mut c) = (0, 0);
        for i in 0..SIZE as usize {
            for j in 0..SIZE as usize {
                if board[i][j] == 'R' {
                    (r, c) = (i as i32, j as i32);
                }
            }
        }

        let mut captures = 0;

        // 向四个方向射线搜索
        for (dr, dc) in DIRS {
            let (mut nr, mut nc) = (r + dr, c + dc);

            while nr >= 0 && nr < SIZE && nc >= 0 && nc < SIZE {
                match board[nr as usize][nc as usize] {
                    'p' => {
                        captures += 1;
                        break; // 吃掉卒，该方向结束
                    }
                    'B' => break, // 被象挡住，该方向结束
                    _ => {}      // 空格，继续
                }
                nr += dr;
                nc += dc;
            }
        }

        captures
    }
}
```
