---
title: "leetcode-矩阵56"
date: 2026-09-19T10:41:35+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 对角线上不同值的数量差

给你一个下标从 0 开始、大小为 m x n 的二维矩阵 grid ，请你求解大小同样为 m x n 的答案矩阵 answer 。

矩阵 answer 中每个单元格 (r, c) 的值可以按下述方式进行计算：

令 topLeft[r][c] 为矩阵 grid 中单元格 (r, c) 左上角对角线上 不同值 的数量。
令 bottomRight[r][c] 为矩阵 grid 中单元格 (r, c) 右下角对角线上 不同值 的数量。

然后 answer[r][c] = |topLeft[r][c] - bottomRight[r][c]| 。

返回矩阵 answer 。

矩阵对角线 是从最顶行或最左列的某个单元格开始，向右下方向走到矩阵末尾的对角线。

如果单元格 (r1, c1) 和单元格 (r, c) 属于同一条对角线且 r1 < r ，则单元格 (r1, c1) 属于单元格 (r, c) 的左上对角线。类似地，可以定义右下对角线。


```
impl Solution {
    /// 每条左上-右下对角线是独立的。对每个格子 (i,j)：
    ///   topLeft    = 同一对角线上、位于 (i,j) 左上方的所有格子的不同值数量
    ///   bottomRight= 同一对角线上、位于 (i,j) 右下方的所有格子的不同值数量
    /// answer = |topLeft - bottomRight|
    ///
    /// 关键优化：按对角线分组预处理。
    /// 每条对角线（用 i-j 标识）从左下往右上扫描时，可增量维护"已见值的集合"，
    /// 但本题需要的是"某格左上/右下的不同值数"，而集合大小无法直接相减，
    /// 因此改为对每条对角线分别正向、反向各扫一遍，记录每个位置的前缀/后缀不同值数。
    /// 时间复杂度 O(m*n)，空间 O(m*n)。
    pub fn difference_of_distinct_values(grid: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let m = grid.len();
        let n = grid[0].len();
        // tl[i][j]: 以 (i,j) 结尾的对角线前缀（含左上所有格）的不同值数
        // br[i][j]: 以 (i,j) 开头的对角线后缀（含右下所有格）的不同值数
        let mut tl = vec![vec![0i32; n]; m];
        let mut br = vec![vec![0i32; n]; m];

        // 正向扫描：i 从小到大、j 从小到大，计算前缀不同值数
        // 每条对角线起点在最顶行或最左列
        for i in 0..m {
            for j in 0..n {
                let mut seen = std::collections::HashSet::new();
                let (mut x, mut y) = (i, j);
                // 回溯到该对角线起点
                while x > 0 && y > 0 {
                    x -= 1;
                    y -= 1;
                }
                // 从起点扫到 (i,j)
                while x <= i {
                    seen.insert(grid[x][y]);
                    x += 1;
                    y += 1;
                }
                tl[i][j] = seen.len() as i32;
            }
        }

        // 反向扫描：i 从大到小、j 从大到小，计算后缀不同值数
        for i in (0..m).rev() {
            for j in (0..n).rev() {
                let mut seen = std::collections::HashSet::new();
                let (mut x, mut y) = (i, j);
                // 从 (i,j) 扫到对角线终点
                while x < m && y < n {
                    seen.insert(grid[x][y]);
                    x += 1;
                    y += 1;
                }
                br[i][j] = seen.len() as i32;
            }
        }

        // 逐格求绝对差
        let mut res = vec![vec![0i32; n]; m];
        for i in 0..m {
            for j in 0..n {
                res[i][j] = (tl[i][j] - br[i][j]).abs();
            }
        }
        res
    }
}
```
