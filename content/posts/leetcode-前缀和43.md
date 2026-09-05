---
title: "leetcode-前缀和43"
date: 2026-08-25T10:02:48+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 最大的幻方

一个 k x k 的 幻方 指的是一个 k x k 填满整数的方格阵，且每一行、每一列以及两条对角线的和 全部相等 。幻方中的整数 不需要互不相同 。显然，每个 1 x 1 的方格都是一个幻方。

给你一个 m x n 的整数矩阵 grid ，请你返回矩阵中 最大幻方 的 尺寸 （即边长 k）。


```
impl Solution {
    /// 返回矩阵中最大幻方的边长
    ///
    /// 幻方：所有行、列、对角线之和相等
    ///
    /// 思路：
    /// 1. 构建行前缀和、列前缀和、主对角线前缀和、副对角线前缀和
    /// 2. 从大到小枚举边长，检查所有可能位置
    /// 3. 使用前缀和 O(1) 计算行、列、对角线和
    pub fn largest_magic_square(grid: Vec<Vec<i32>>) -> i32 {
        let m = grid.len();
        let n = grid[0].len();
        let max_edge = m.min(n);

        // 行前缀和：row[i][j] = grid[i][0..=j] 的和
        let mut row_pref = vec![vec![0; n + 1]; m];
        for i in 0..m {
            for j in 0..n {
                row_pref[i][j + 1] = row_pref[i][j] + grid[i][j];
            }
        }

        // 列前缀和：col[i][j] = grid[0..=i][j] 的和
        let mut col_pref = vec![vec![0; n]; m + 1];
        for j in 0..n {
            for i in 0..m {
                col_pref[i + 1][j] = col_pref[i][j] + grid[i][j];
            }
        }

        // 主对角线前缀和：diag1[i][j] = 从 grid[i][j] 开始向左上方的对角线之和
        let mut diag1 = vec![vec![0; n]; m];
        for i in 0..m {
            for j in 0..n {
                diag1[i][j] = grid[i][j];
                if i > 0 && j > 0 {
                    diag1[i][j] += diag1[i - 1][j - 1];
                }
            }
        }

        // 副对角线前缀和：diag2[i][j] = 从 grid[i][j] 开始向右上方的对角线之和
        let mut diag2 = vec![vec![0; n]; m];
        for i in 0..m {
            for j in (0..n).rev() {
                diag2[i][j] = grid[i][j];
                if i > 0 && j + 1 < n {
                    diag2[i][j] += diag2[i - 1][j + 1];
                }
            }
        }

        // 从大到小枚举边长
        for edge in (2..=max_edge).rev() {
            let e = edge as i32;
            for i in 0..=(m - edge) {
                for j in 0..=(n - edge) {
                    // 计算第一行和作为标准值
                    let target = row_pref[i][j + edge] - row_pref[i][j];

                    // 检查所有行
                    let mut valid = true;
                    for r in i..i + edge {
                        if row_pref[r][j + edge] - row_pref[r][j] != target {
                            valid = false;
                            break;
                        }
                    }
                    if !valid { continue; }

                    // 检查所有列
                    for c in j..j + edge {
                        if col_pref[i + edge][c] - col_pref[i][c] != target {
                            valid = false;
                            break;
                        }
                    }
                    if !valid { continue; }

                    // 检查主对角线：从左上到右下
                    let d1 = diag1[i + edge - 1][j + edge - 1]
                        - if i > 0 && j > 0 { diag1[i - 1][j - 1] } else { 0 };

                    // 检查副对角线：从右上到左下
                    let d2 = diag2[i + edge - 1][j]
                        - if i > 0 && j + edge < n { diag2[i - 1][j + edge] } else { 0 };

                    if d1 == target && d2 == target {
                        return edge as i32;
                    }
                }
            }
        }

        1 // 至少 1x1 幻方
    }
}
```
