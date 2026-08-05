---
title: "leetcode-枚举83"
date: 2026-07-09T10:05:03+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 等和矩阵分割 II

给你一个由正整数组成的 m x n 矩阵 grid。你的任务是判断是否可以通过 一条水平或一条垂直分割线 将矩阵分割成两部分，使得：

分割后形成的每个部分都是 非空 的。

两个部分中所有元素的和 相等 ，或者总共 最多移除一个单元格 （从其中一个部分中）的情况下可以使它们相等。

如果移除某个单元格，剩余部分必须保持 连通 。

如果存在这样的分割，返回 true；否则，返回 false。

注意： 如果一个部分中的每个单元格都可以通过向上、向下、向左或向右移动到达同一部分中的其他单元格，则认为这一部分是 连通 的。


```
use std::collections::HashSet;

impl Solution {
    /// 将矩阵顺时针旋转 90 度
    fn rotate_clockwise(grid: &Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let m = grid.len();
        let n = grid[0].len();
        let mut rotated = vec![vec![0; m]; n];

        for i in 0..m {
            for j in 0..n {
                rotated[j][m - 1 - i] = grid[i][j];
            }
        }
        rotated
    }

    pub fn can_partition_grid(mut grid: Vec<Vec<i32>>) -> bool {
        // 计算矩阵总元素和
        let total: i64 = grid.iter().flat_map(|row| row.iter()).map(|&x| x as i64).sum();

        // 尝试 4 个旋转方向（原始 + 旋转 90/180/270 度）
        for _ in 0..4 {
            let m = grid.len();
            let n = grid[0].len();

            // 如果行数小于 2，旋转后继续尝试
            if m < 2 {
                grid = Self::rotate_clockwise(&grid);
                continue;
            }

            // 处理单列情况：只能水平分割
            if n == 1 {
                let mut prefix_sum = 0i64;
                for i in 0..m - 1 {
                    prefix_sum += grid[i][0] as i64;
                    let diff = prefix_sum * 2 - total;

                    // 检查三种情况：完全相等，或移除第一个/当前单元格后相等
                    if diff == 0 || diff == grid[0][0] as i64 || diff == grid[i][0] as i64 {
                        return true;
                    }
                }
                grid = Self::rotate_clockwise(&grid);
                continue;
            }

            // 处理多列情况
            let mut seen_values = HashSet::new();
            seen_values.insert(0); // 空集，用于处理不需要移除任何单元格的情况
            let mut prefix_sum = 0i64;

            // 枚举水平分割线：在第 i 行下方分割
            for i in 0..m - 1 {
                // 将第 i 行的所有元素加入前缀和
                for j in 0..n {
                    let val = grid[i][j] as i64;
                    prefix_sum += val;
                    seen_values.insert(val);
                }

                // 计算需要移除的值才能使两部分相等
                // 设上半部分和为 sum，总数为 total
                // 如果移除单元格值为 x，则 sum - x = (total - sum) 或 sum = (total - sum) - x
                // 两种情况可统一为：x = 2*sum - total 或 x = total - 2*sum
                // 即 diff = |2*sum - total|，需要 seen_values.contains(&diff)
                let diff = prefix_sum * 2 - total;

                // 处理第一行特殊情况：可以移除第一行的第一个或最后一个单元格
                if i == 0 {
                    if diff == 0 || diff == grid[0][0] as i64 || diff == grid[0][n - 1] as i64 {
                        return true;
                    }
                    continue;
                }

                // 检查是否存在可以移除的单元格
                if seen_values.contains(&diff) {
                    return true;
                }
            }

            // 旋转矩阵，尝试下一个方向
            grid = Self::rotate_clockwise(&grid);
        }

        false
    }
}
```
