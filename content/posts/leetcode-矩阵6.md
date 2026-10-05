---
title: "leetcode-矩阵6"
date: 2026-09-19T10:41:32+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 搜索二维矩阵

给你一个满足下述两条属性的 m x n 整数矩阵：

每行中的整数从左到右按非严格递增顺序排列。
每行的第一个整数大于前一行的最后一个整数。

给你一个整数 target ，如果 target 在矩阵中，返回 true ；否则，返回 false 。

你必须编写一个时间复杂度为 O(log(m * n)) 的解决方案。


```
impl Solution {
    pub fn search_matrix(matrix: Vec<Vec<i32>>, target: i32) -> bool {
        let m = matrix.len();
        let n = matrix[0].len();

        // 矩阵按行展平后是一个整体有序的一维数组，
        // 因此可以对 "虚拟一维数组" 直接做二分查找，O(log(m*n))。
        let (mut lo, mut hi) = (0i32, (m * n) as i32 - 1);
        while lo <= hi {
            let mid = lo + (hi - lo) / 2;
            // 一维下标 mid 映射回二维：行 = mid / n，列 = mid % n
            let val = matrix[mid as usize / n][mid as usize % n];
            match val.cmp(&target) {
                std::cmp::Ordering::Equal => return true,
                std::cmp::Ordering::Less => lo = mid + 1,
                std::cmp::Ordering::Greater => hi = mid - 1,
            }
        }

        false
    }
}
```
