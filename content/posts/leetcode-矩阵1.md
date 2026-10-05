---
title: "leetcode-矩阵1"
date: 2026-09-19T10:41:32+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 有效的数独

请你判断一个 9 x 9 的数独是否有效。只需要 根据以下规则 ，验证已经填入的数字是否有效即可。

数字 1-9 在每一行只能出现一次。
数字 1-9 在每一列只能出现一次。
数字 1-9 在每一个以粗实线分隔的 3x3 宫内只能出现一次。（请参考示例图）


注意：

一个有效的数独（部分已被填充）不一定是可解的。
只需要根据以上规则，验证已经填入的数字是否有效即可。
空白格用 '.' 表示。


```
impl Solution {
    pub fn is_valid_sudoku(board: Vec<Vec<char>>) -> bool {
        // 用一维数组代替二维数组，提升缓存友好度并减少内存开销。
        // rows[i * 9 + d] 表示第 i 行数字 d+1 是否已出现
        // cols[j * 9 + d] 表示第 j 列数字 d+1 是否已出现
        // boxes[b * 9 + d] 表示第 b 个 3x3 宫数字 d+1 是否已出现
        let mut seen = [false; 9 * 9 * 3];

        for i in 0..9 {
            for j in 0..9 {
                let c = board[i][j];
                if c == '.' {
                    continue;
                }

                let d = (c as u8 - b'1') as usize; // 数字索引 0..8
                let b = (i / 3) * 3 + (j / 3);     // 3x3 宫索引 0..8

                // 分别计算三个标记在 seen 中的位置
                let row_idx = i * 9 + d;
                let col_idx = 9 * 9 + j * 9 + d;
                let box_idx = 9 * 9 * 2 + b * 9 + d;

                if seen[row_idx] || seen[col_idx] || seen[box_idx] {
                    return false;
                }

                seen[row_idx] = true;
                seen[col_idx] = true;
                seen[box_idx] = true;
            }
        }

        true
    }
}
```
