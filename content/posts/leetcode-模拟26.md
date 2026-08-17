---
title: "leetcode-模拟26"
date: 2026-08-08T11:31:09+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 可以攻击国王的皇后

在一个 下标从 0 开始 的 8 x 8 棋盘上，可能有多个黑皇后和一个白国王。

给你一个二维整数数组 queens，其中 queens[i] = [xQueeni, yQueeni] 表示第 i 个黑皇后在棋盘上的位置。还给你一个长度为 2 的整数数组 king，其中 king = [xKing, yKing] 表示白国王的位置。

返回 能够直接攻击国王的黑皇后的坐标。你可以以 任何顺序 返回答案。


```
impl Solution {
    /// 返回能直接攻击国王的黑皇后坐标
    /// 核心思路：从国王出发，向8个方向射线搜索，遇到的第一个皇后即为可攻击者
    /// 时间复杂度: O(8 * 8) = O(1)  空间复杂度: O(q)
    pub fn queens_attackthe_king(queens: Vec<Vec<i32>>, king: Vec<i32>) -> Vec<Vec<i32>> {
        // 8个方向：左、左下、下、右下、右、右上、上、左上
        const DIRS: [(i32, i32); 8] = [
            (0, -1), (1, -1), (1, 0), (1, 1),
            (0, 1), (-1, 1), (-1, 0), (-1, -1),
        ];
        const SIZE: i32 = 8;

        let (kx, ky) = (king[0], king[1]);

        // 用位掩码编码皇后位置：x * 8 + y，比 HashSet<(i32, i32)> 更快更省内存
        let mut queen_mask = [false; 64];
        for q in &queens {
            queen_mask[(q[0] * SIZE + q[1]) as usize] = true;
        }

        let mut ans = Vec::with_capacity(8);

        for (dx, dy) in DIRS {
            let (mut x, mut y) = (kx + dx, ky + dy);

            while x >= 0 && x < SIZE && y >= 0 && y < SIZE {
                if queen_mask[(x * SIZE + y) as usize] {
                    ans.push(vec![x, y]);
                    break;
                }
                x += dx;
                y += dy;
            }
        }

        ans
    }
}
```
