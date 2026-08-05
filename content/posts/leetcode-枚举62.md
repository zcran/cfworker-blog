---
title: "leetcode-枚举62"
date: 2026-07-09T10:05:02+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 包含所有 1 的最小矩形面积 II

给你一个二维 二进制 数组 grid。你需要找到 3 个 不重叠、面积 非零 、边在水平方向和竖直方向上的矩形，并且满足 grid 中所有的 1 都在这些矩形的内部。

返回这些矩形面积之和的 最小 可能值。

注意，这些矩形可以相接。


```
impl Solution {
    /// 计算指定矩形区域内所有 1 的最小包围盒面积
    ///
    /// # 参数
    /// - `grid`: 二维二进制数组
    /// - `u, d`: 矩形的上下边界（包含）
    /// - `l, r`: 矩形的左右边界（包含）
    ///
    /// # 返回值
    /// 如果区域内没有 1，返回 `i32::MAX / 3`（表示无效）
    /// 否则返回包围盒的面积（高度 × 宽度）
    fn minimum_sum2(grid: &Vec<Vec<i32>>, u: usize, d: usize, l: usize, r: usize) -> i32 {
        let mut min_row = grid.len();
        let mut max_row = 0;
        let mut min_col = grid[0].len();
        let mut max_col = 0;

        // 遍历矩形区域，找到所有 1 的边界
        for row in u..=d {
            for col in l..=r {
                if grid[row][col] == 1 {
                    min_row = min_row.min(row);
                    max_row = max_row.max(row);
                    min_col = min_col.min(col);
                    max_col = max_col.max(col);
                }
            }
        }

        // 如果存在 1，返回包围盒面积；否则返回无效值
        if min_row <= max_row {
            ((max_row - min_row + 1) * (max_col - min_col + 1)) as i32
        } else {
            i32::MAX / 3
        }
    }

    /// 将矩阵逆时针旋转 90 度
    ///
    /// 旋转后原矩阵的 (i, j) 位置移动到 (m-1-j, i)
    /// 其中 m 是原矩阵的列数
    fn rotate(vec: &Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let rows = vec.len();
        let cols = if rows > 0 { vec[0].len() } else { 0 };
        let mut rotated = vec![vec![0; rows]; cols];

        for i in 0..rows {
            for j in 0..cols {
                rotated[cols - j - 1][i] = vec[i][j];
            }
        }

        rotated
    }

    /// 在给定网格上求解三个不重叠矩形的最小面积和
    ///
    /// 枚举两种分割模式：
    /// 1. L 形分割：用一条水平线和一条垂直线将网格分为四个象限，取三个象限
    /// 2. 三个水平条：用两条水平线将网格分为三个水平区域
    fn solve(grid: &Vec<Vec<i32>>) -> i32 {
        let rows = grid.len();
        let cols = if rows > 0 { grid[0].len() } else { 0 };
        let mut min_area = (rows * cols) as i32;

        // === L 形分割 ===
        // 遍历所有可能的水平分割线 i 和垂直分割线 j
        for h in 0..rows - 1 {
            for v in 0..cols - 1 {
                // 方案 A：取右上 + 左下 + 右下（去掉左上）
                let area_a = Self::minimum_sum2(grid, 0, h, 0, cols - 1)      // 右上
                    + Self::minimum_sum2(grid, h + 1, rows - 1, 0, v)         // 左下
                    + Self::minimum_sum2(grid, h + 1, rows - 1, v + 1, cols - 1); // 右下
                min_area = min_area.min(area_a);

                // 方案 B：取左上 + 右上 + 左下（去掉右下）
                let area_b = Self::minimum_sum2(grid, 0, h, 0, v)             // 左上
                    + Self::minimum_sum2(grid, 0, h, v + 1, cols - 1)         // 右上
                    + Self::minimum_sum2(grid, h + 1, rows - 1, 0, cols - 1); // 左下
                min_area = min_area.min(area_b);
            }
        }

        // === 三个水平条 ===
        // 遍历两条水平分割线 i 和 j
        for h1 in 0..rows - 2 {
            for h2 in h1 + 1..rows - 1 {
                let area = Self::minimum_sum2(grid, 0, h1, 0, cols - 1)           // 上条
                    + Self::minimum_sum2(grid, h1 + 1, h2, 0, cols - 1)          // 中条
                    + Self::minimum_sum2(grid, h2 + 1, rows - 1, 0, cols - 1);   // 下条
                min_area = min_area.min(area);
            }
        }

        min_area
    }

    /// 主函数：返回覆盖所有 1 的三个不重叠矩形的最小面积和
    ///
    /// 先对原始网格求解，再旋转 90 度后求解（覆盖三个垂直条的情况）
    /// 取两次结果的最小值
    pub fn minimum_sum(grid: Vec<Vec<i32>>) -> i32 {
        let rotated = Self::rotate(&grid);
        Self::solve(&grid).min(Self::solve(&rotated))
    }
}
```
