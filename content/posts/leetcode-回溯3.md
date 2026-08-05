---
title: "leetcode-回溯3"
date: 2026-07-04T10:22:01+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 解数独

编写一个程序，通过填充空格来解决数独问题。

数独的解法需 遵循如下规则：

1.数字 1-9 在每一行只能出现一次。
2.数字 1-9 在每一列只能出现一次。
3.数字 1-9 在每一个以粗实线分隔的 3x3 宫内只能出现一次。（请参考示例图）

数独部分空格内已填入了数字，空白格用 '.' 表示。


```
impl Solution {
    /// 解数独问题
    ///
    /// 使用位掩码 + MRV（最小剩余值）启发式优化算法
    ///
    /// # 算法说明
    /// - 位掩码：用 u16 的 9 个二进制位表示数字 1-9 的使用状态
    /// - MRV 优化：每次选择候选数字最少的空格填充，大幅减少搜索分支
    ///
    /// # 复杂度
    /// - 时间复杂度：O(9^81)，但实际运行很快（启发式剪枝）
    /// - 空间复杂度：O(81)
    pub fn solve_sudoku(board: &mut Vec<Vec<char>>) {
        // ========== 1. 初始化状态数组 ==========
        // 使用位掩码记录每行已使用的数字（第 i 位为 1 表示数字 i+1 已被使用）
        let mut row_mask = [0u16; 9];
        let mut col_mask = [0u16; 9];
        let mut box_mask = [0u16; 9];

        // 收集所有空格的位置
        let mut empty_cells = Vec::new();

        // ========== 2. 预处理：初始化状态并收集空格 ==========
        for row in 0..9 {
            for col in 0..9 {
                if board[row][col] == '.' {
                    empty_cells.push((row, col));
                } else {
                    // 将字符数字转换为位掩码
                    let digit = board[row][col].to_digit(10).unwrap() as u16;
                    let bit = 1 << digit;

                    row_mask[row] |= bit;
                    col_mask[col] |= bit;
                    box_mask[(row / 3) * 3 + col / 3] |= bit;
                }
            }
        }

        // ========== 3. 回溯搜索函数 ==========
        /// 深度优先搜索数独解
        ///
        /// # 参数
        /// - `board`: 数独棋盘
        /// - `empty_cells`: 所有空格坐标列表
        /// - `index`: 当前处理的空格索引
        /// - `row_mask/col_mask/box_mask`: 行/列/宫的使用状态
        ///
        /// # 返回
        /// - `true`: 找到有效解
        /// - `false`: 当前路径无解
        fn backtrack(
            board: &mut Vec<Vec<char>>,
            empty_cells: &mut [(usize, usize)],
            index: usize,
            row_mask: &mut [u16; 9],
            col_mask: &mut [u16; 9],
            box_mask: &mut [u16; 9],
        ) -> bool {
            // 所有空格都已填满，找到解
            if index == empty_cells.len() {
                return true;
            }

            // ----- MRV 优化：选择候选数字最少的空格 -----
            let mut min_candidates = 10;
            let mut best_idx = index;

            // 在剩余空格中查找最优位置
            for i in index..empty_cells.len() {
                let (row, col) = empty_cells[i];

                // 计算该空格已使用的数字（行 | 列 | 宫）
                let used_mask = row_mask[row] | col_mask[col] | box_mask[(row / 3) * 3 + col / 3];
                // 候选数字数量 = 9 - 已使用数字数量
                let candidates = 9 - used_mask.count_ones() as usize;

                // 更新最小候选数位置
                if candidates < min_candidates {
                    min_candidates = candidates;
                    best_idx = i;

                    // 如果只有一个候选数字，无需继续查找（已经最优）
                    if candidates == 1 {
                        break;
                    }
                }
            }

            // 将最优位置交换到当前索引
            empty_cells.swap(index, best_idx);
            let (row, col) = empty_cells[index];
            let box_idx = (row / 3) * 3 + col / 3;

            // ----- 尝试填入数字 1-9 -----
            for digit in 1..=9 {
                let bit = 1 << digit;

                // 检查数字是否已被行/列/宫使用
                let is_used = (row_mask[row] & bit) != 0
                           || (col_mask[col] & bit) != 0
                           || (box_mask[box_idx] & bit) != 0;

                if !is_used {
                    // ---- 填入数字 ----
                    board[row][col] = char::from_digit(digit, 10).unwrap();
                    row_mask[row] |= bit;
                    col_mask[col] |= bit;
                    box_mask[box_idx] |= bit;

                    // 递归处理下一个空格
                    if backtrack(board, empty_cells, index + 1, row_mask, col_mask, box_mask) {
                        return true; // 找到解，立即返回
                    }

                    // ---- 回溯：撤销刚才的填入 ----
                    board[row][col] = '.';
                    row_mask[row] ^= bit; // 使用 XOR 移除位
                    col_mask[col] ^= bit;
                    box_mask[box_idx] ^= bit;
                }
            }

            // 所有数字尝试失败，当前路径无解
            false
        }

        // ========== 4. 启动回溯搜索 ==========
        backtrack(
            board,
            &mut empty_cells,
            0,
            &mut row_mask,
            &mut col_mask,
            &mut box_mask,
        );
    }
}
```
