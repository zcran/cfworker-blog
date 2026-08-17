---
title: "leetcode-模拟20"
date: 2026-08-08T11:31:09+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 螺旋矩阵 III

在 rows x cols 的网格上，你从单元格 (rStart, cStart) 面朝东面开始。网格的西北角位于第一行第一列，网格的东南角位于最后一行最后一列。

你需要以顺时针按螺旋状行走，访问此网格中的每个位置。每当移动到网格的边界之外时，需要继续在网格之外行走（但稍后可能会返回到网格边界）。

最终，我们到过网格的所有 rows x cols 个空间。

按照访问顺序返回表示网格位置的坐标列表。


```
impl Solution {
    /// 螺旋遍历矩阵：从 (rStart, cStart) 出发，顺时针螺旋访问所有网格单元
    /// 核心规律：每走两步，步长 +1（右1→下1→左2→上2→右3→下3...）
    /// 时间复杂度: O(max(rows, cols)²)  空间复杂度: O(rows * cols)
    pub fn spiral_matrix_iii(rows: i32, cols: i32, r_start: i32, c_start: i32) -> Vec<Vec<i32>> {
        // 方向向量：东、南、西、北（顺时针）
        const DIRS: [(i32, i32); 4] = [(0, 1), (1, 0), (0, -1), (-1, 0)];

        let total = (rows * cols) as usize;
        let mut result = Vec::with_capacity(total);

        let (mut r, mut c) = (r_start, c_start);
        let mut dir = 0usize;      // 当前方向索引
        let mut step_len = 1;      // 当前步长
        let mut step_count = 0;    // 当前步长已走的段数（每步长走2段后增加）

        // 起始点先入结果
        result.push(vec![r, c]);

        while result.len() < total {
            let (dr, dc) = DIRS[dir];

            // 沿当前方向走 step_len 步
            for _ in 0..step_len {
                r += dr;
                c += dc;

                // 只记录网格内的坐标
                if r >= 0 && r < rows && c >= 0 && c < cols {
                    result.push(vec![r, c]);
                }
            }

            // 转向（顺时针）
            dir = (dir + 1) & 3;

            // 每走两段后步长 +1
            step_count += 1;
            if step_count == 2 {
                step_len += 1;
                step_count = 0;
            }
        }

        result
    }
}
```
