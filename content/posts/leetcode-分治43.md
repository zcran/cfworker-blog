---
title: "leetcode-分治43"
date: 2026-06-13T10:51:39+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 排序矩阵查找

给定 M×N 矩阵，每一行、每一列都按升序排列，请编写代码找出某元素。

示例：

现有矩阵 matrix 如下：

[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
给定 target = 5，返回 true。

给定 target = 20，返回 false。

```
impl Solution {
    /// 在行列均升序排列的矩阵中搜索目标值
    ///
    /// # 算法思路
    /// 从矩阵的**右上角**开始：
    /// - 若当前值 > target，则左移一列（当前列下方的值都更大，不可能包含target）
    /// - 若当前值 < target，则下移一行（当前行左侧的值都更小，不可能包含target）
    /// - 若相等，返回 true
    ///
    /// # 复杂度
    /// - 时间复杂度: O(m + n)，最多移动 m+n 步
    /// - 空间复杂度: O(1)，仅使用常量空间
    ///
    /// # 示例
    /// ```
    /// let matrix = vec![
    ///     vec![1, 4, 7, 11, 15],
    ///     vec![2, 5, 8, 12, 19],
    /// ];
    /// assert_eq!(Solution::search_matrix(matrix, 5), true);
    /// ```
    pub fn search_matrix(matrix: Vec<Vec<i32>>, target: i32) -> bool {
        // 处理空矩阵
        if matrix.is_empty() || matrix[0].is_empty() {
            return false;
        }

        let rows = matrix.len();
        let cols = matrix[0].len();

        // 从右上角开始
        let mut row = 0;
        let mut col = cols - 1;

        while row < rows && col < cols {
            let current = matrix[row][col];

            if current == target {
                return true;
            } else if current > target {
                // 当前值偏大，左移（排除当前列）
                col -= 1;
            } else {
                // 当前值偏小，下移（排除当前行）
                row += 1;
            }
        }

        false
    }
}
```
