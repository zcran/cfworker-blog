---
title: "leetcode-广度优先搜索49"
date: 2026-09-13T09:32:09+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 黑白翻转棋

在 n*m 大小的棋盘中，有黑白两种棋子，黑棋记作字母 "X", 白棋记作字母 "O"，空余位置记作 "."。当落下的棋子与其他相同颜色的棋子在行、列或对角线完全包围（中间不存在空白位置）另一种颜色的棋子，则可以翻转这些棋子的颜色。

力扣挑战赛」黑白翻转棋项目中，将提供给选手一个未形成可翻转棋子的棋盘残局，其状态记作 chessboard。若下一步可放置一枚黑棋，请问选手最多能翻转多少枚白棋。

注意：

若翻转白棋成黑棋后，棋盘上仍存在可以翻转的白棋，将可以 继续 翻转白棋
输入数据保证初始棋盘状态无可以翻转的棋子且存在空余位置

```
impl Solution {
    pub fn flip_chess(chessboard: Vec<String>) -> i32 {
        // 把棋盘转成可变的字节网格
        let mut grid: Vec<Vec<u8>> = chessboard
            .iter()
            .map(|s| s.as_bytes().to_vec())
            .collect();
        let (n, m) = (grid.len(), grid[0].len());

        const DIRS: [(isize, isize); 8] = [
            (0, 1), (0, -1), (1, 0), (-1, 0),
            (1, 1), (1, -1), (-1, 1), (-1, -1),
        ];

        let mut best = 0;

        // 枚举所有可以落黑棋的空位
        for row in 0..n {
            for col in 0..m {
                if grid[row][col] != b'.' {
                    continue;
                }

                // 尝试在 (row, col) 落一颗黑棋，并连锁翻转
                grid[row][col] = b'X';
                let mut flipped: Vec<(usize, usize)> = Vec::new();
                let mut stack = vec![(row, col)];

                // BFS：每落下一个黑子，检查 8 个方向能否夹住白棋
                while let Some((x, y)) = stack.pop() {
                    for &(dx, dy) in &DIRS {
                        // 沿该方向找到本方向上的最后一个黑子位置
                        let mut nx = x as isize + dx;
                        let mut ny = y as isize + dy;
                        let mut last_black: Option<(usize, usize)> = None;

                        while nx >= 0 && ny >= 0 && nx < n as isize && ny < m as isize {
                            let (ux, uy) = (nx as usize, ny as usize);
                            match grid[ux][uy] {
                                b'.' => break,              // 遇到空位，方向中断
                                b'X' => last_black = Some((ux, uy)),
                                _ => {}                     // 白棋，继续推进
                            }
                            nx += dx;
                            ny += dy;
                        }

                        // 该方向上必须存在黑子，且不是起点自身
                        let Some((lx, ly)) = last_black else { continue };
                        if (lx, ly) == (x, y) {
                            continue;
                        }

                        // 把起点到该黑子之间的白棋翻黑，并入队继续连锁
                        let mut nx = x as isize + dx;
                        let mut ny = y as isize + dy;
                        while (nx as usize, ny as usize) != (lx, ly) {
                            let (ux, uy) = (nx as usize, ny as usize);
                            if grid[ux][uy] == b'O' {
                                grid[ux][uy] = b'X';
                                flipped.push((ux, uy));
                                stack.push((ux, uy));
                            }
                            nx += dx;
                            ny += dy;
                        }
                    }
                }

                best = best.max(flipped.len() as i32);

                // 还原棋盘：撤销本次落子与所有翻转
                grid[row][col] = b'.';
                for &(x, y) in &flipped {
                    grid[x][y] = b'O';
                }
            }
        }

        best
    }
}
```
