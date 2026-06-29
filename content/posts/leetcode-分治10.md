---
title: "leetcode-分治10"
date: 2026-06-13T10:51:38+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 搜索二维矩阵 II


编写一个高效的算法来搜索 m x n 矩阵 matrix 中的一个目标值 target 。该矩阵具有以下特性：

每行的元素从左到右升序排列。
每列的元素从上到下升序排列。



```
impl Solution {
    /// 搜索二维矩阵（Z 字形查找）
    ///
    /// # 前提条件
    /// 矩阵每行从左到右递增，每列从上到下递增
    ///
    /// # 算法原理
    /// 从右上角开始查找：
    /// - 相等 → 找到目标
    /// - 大于目标 → 排除当前列（该列下方元素都更大）
    /// - 小于目标 → 排除当前行（该行左侧元素都更小）
    ///
    /// # 复杂度
    /// - 时间：O(m + n)
    /// - 空间：O(1)
    ///
    /// # 示例
    /// ```
    /// matrix = [
    ///   [1, 4, 7, 11],
    ///   [2, 5, 8, 12],
    ///   [3, 6, 9, 16]
    /// ]
    /// target = 5
    /// 路径：7(>5左移) → 4(<5下移) → 5(找到) ✓
    /// ```
    pub fn search_matrix(matrix: Vec<Vec<i32>>, target: i32) -> bool {
        // 边界检查
        if matrix.is_empty() || matrix[0].is_empty() {
            return false;
        }

        let rows = matrix.len();
        let cols = matrix[0].len();

        // 从右上角开始搜索
        let mut row = 0;
        let mut col = cols - 1;

        while row < rows && col < cols {
            match matrix[row][col].cmp(&target) {
                std::cmp::Ordering::Equal => return true,
                std::cmp::Ordering::Less => row += 1, // 排除当前行
                std::cmp::Ordering::Greater => col -= 1, // 排除当前列
            }
        }

        false
    }
}
```
