---
title: "leetcode-双指针120"
date: 2026-09-05T09:53:08+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 垂直翻转子矩阵

给你一个 m x n 的整数矩阵 grid，以及三个整数 x、y 和 k。

整数 x 和 y 表示一个 正方形子矩阵 的左上角下标，整数 k 表示该正方形子矩阵的边长。

你的任务是垂直翻转子矩阵的行顺序。

返回更新后的矩阵。


```
impl Solution {
    /// 将以 (x, y) 为左上角、边长为 k 的正方形子矩阵的行顺序垂直翻转。
    ///
    /// 思路：子矩阵的第 i 行与第 k-1-i 行整体交换，逐列对调即可。
    /// 时间复杂度 O(k^2)，空间复杂度 O(1)。
    pub fn reverse_submatrix(mut grid: Vec<Vec<i32>>, x: i32, y: i32, k: i32) -> Vec<Vec<i32>> {
        let (x, y, k) = (x as usize, y as usize, k as usize);

        // 上下对称的两行逐列交换
        for i in 0..k / 2 {
            let top = x + i;
            let bottom = x + k - 1 - i;

            for j in y..y + k {
                let tmp = grid[top][j];
                grid[top][j] = grid[bottom][j];
                grid[bottom][j] = tmp;
            }
        }

        grid
    }
}
```
