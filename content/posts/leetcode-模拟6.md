---
title: "leetcode-模拟6"
date: 2026-08-08T11:31:08+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 生命游戏

根据 百度百科 ， 生命游戏 ，简称为 生命 ，是英国数学家约翰·何顿·康威在 1970 年发明的细胞自动机。

给定一个包含 m × n 个格子的面板，每一个格子都可以看成是一个细胞。每个细胞都具有一个初始状态： 1 即为 活细胞 （live），或 0 即为 死细胞 （dead）。每个细胞与其八个相邻位置（水平，垂直，对角线）的细胞都遵循以下四条生存定律：

如果活细胞周围八个位置的活细胞数少于两个，则该位置活细胞死亡；
如果活细胞周围八个位置有两个或三个活细胞，则该位置活细胞仍然存活；
如果活细胞周围八个位置有超过三个活细胞，则该位置活细胞死亡；
如果死细胞周围正好有三个活细胞，则该位置死细胞复活；
下一个状态是通过将上述规则同时应用于当前状态下的每个细胞所形成的，其中细胞的出生和死亡是 同时 发生的。给你 m x n 网格面板 board 的当前状态，返回下一个状态。

给定当前 board 的状态，更新 board 到下一个状态。

注意 你不需要返回任何东西。


```
impl Solution {
    pub fn game_of_life(board: &mut Vec<Vec<i32>>) {
        let m = board.len();
        let n = board[0].len();

        // 用中间状态编码：2=死→活, 3=活→死
        // 这样既能读取原始状态，又能标记新状态
        for i in 0..m {
            for j in 0..n {
                let live = Self::count_live(board, i, j, m, n);
                let curr = board[i][j];
                if curr == 1 && (live < 2 || live > 3) {
                    board[i][j] = 3; // 活→死
                } else if curr == 0 && live == 3 {
                    board[i][j] = 2; // 死→活
                }
            }
        }

        // 解码最终状态
        for i in 0..m {
            for j in 0..n {
                board[i][j] = match board[i][j] {
                    2 => 1,
                    3 => 0,
                    other => other,
                };
            }
        }
    }

    // 统计周围活细胞数，利用编码状态判断原始值
    fn count_live(board: &[Vec<i32>], i: usize, j: usize, m: usize, n: usize) -> i32 {
        let mut count = 0;
        for di in [-1isize, 0, 1] {
            for dj in [-1isize, 0, 1] {
                if di == 0 && dj == 0 {
                    continue;
                }
                let ni = i as isize + di;
                let nj = j as isize + dj;
                if ni >= 0 && ni < m as isize && nj >= 0 && nj < n as isize {
                    let val = board[ni as usize][nj as usize];
                    // 原始为1：1 或 3（活→死）；原始为0：0 或 2（死→活）
                    if val == 1 || val == 3 {
                        count += 1;
                    }
                }
            }
        }
        count
    }
}
```
