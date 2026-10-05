---
title: "leetcode-矩阵17"
date: 2026-09-19T10:41:33+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 矩阵中的幻方

3 x 3 的幻方是一个填充有 从 1 到 9  的不同数字的 3 x 3 矩阵，其中每行，每列以及两条对角线上的各数之和都相等。

给定一个由整数组成的row x col 的 grid，其中有多少个 3 × 3 的 “幻方” 子矩阵？

注意：虽然幻方只能包含 1 到 9 的数字，但 grid 可以包含最多15的数字。


```
impl Solution {
    pub fn num_magic_squares_inside(grid: Vec<Vec<i32>>) -> i32 {
        let rows = grid.len();
        let cols = grid[0].len();

        if rows < 3 || cols < 3 {
            return 0;
        }

        // 3x3 幻方中心必为 5，且 1~9 各出现一次、每条线和为 15。
        // 由于幻方必须是 1~9 的排列，所有合法幻方本质相同（仅旋转/翻转），
        // 因此只需逐一验证候选子矩阵。
        let mut count = 0;

        for r in 0..rows - 2 {
            for c in 0..cols - 2 {
                // 中心必须为 5，可快速排除绝大多数候选
                if grid[r + 1][c + 1] != 5 {
                    continue;
                }

                // 九个值依次取出，便于统一校验
                let v = [
                    grid[r][c],     grid[r][c + 1],     grid[r][c + 2],
                    grid[r + 1][c], grid[r + 1][c + 1], grid[r + 1][c + 2],
                    grid[r + 2][c], grid[r + 2][c + 1], grid[r + 2][c + 2],
                ];

                // 用位掩码同时完成 "1~9 各一次" 与范围检查：
                // mask 的位 k 表示数字 k 是否出现；要求恰好覆盖 1~9。
                let mut mask = 0u16;
                let mut ok = true;
                for &x in &v {
                    if x < 1 || x > 9 {
                        ok = false;
                        break;
                    }
                    let bit = 1u16 << x;
                    if mask & bit != 0 {
                        ok = false;
                        break;
                    }
                    mask |= bit;
                }
                // 0b11_1111_1110 表示位 1~9 全部置位
                if !ok || mask != 0b11_1111_1110 {
                    continue;
                }

                // 校验各行、各列、两对角线之和是否为 15
                if v[0] + v[1] + v[2] == 15
                    && v[3] + v[4] + v[5] == 15
                    && v[6] + v[7] + v[8] == 15
                    && v[0] + v[3] + v[6] == 15
                    && v[1] + v[4] + v[7] == 15
                    && v[2] + v[5] + v[8] == 15
                    && v[0] + v[4] + v[8] == 15
                    && v[2] + v[4] + v[6] == 15
                {
                    count += 1;
                }
            }
        }

        count
    }
}
```
