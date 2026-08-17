---
title: "leetcode-模拟28"
date: 2026-08-08T11:31:10+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 二维网格迁移

给你一个 m 行 n 列的二维网格 grid 和一个整数 k。你需要将 grid 迁移 k 次。

每次「迁移」操作将会引发下述活动：

位于 grid[i][j]（j < n - 1）的元素将会移动到 grid[i][j + 1]。
位于 grid[i][n - 1] 的元素将会移动到 grid[i + 1][0]。
位于 grid[m - 1][n - 1] 的元素将会移动到 grid[0][0]。

请你返回 k 次迁移操作后最终得到的 二维网格。


```
impl Solution {
    /// 将二维网格按行优先顺序整体右移 k 位
    /// 核心思路：展平为一维，计算偏移后回填
    /// 时间复杂度: O(m*n)  空间复杂度: O(m*n)
    pub fn shift_grid(grid: Vec<Vec<i32>>, k: i32) -> Vec<Vec<i32>> {
        let rows = grid.len();
        let cols = grid[0].len();
        let total = rows * cols;
        let k = (k as usize) % total; // 处理 k >= total 的情况

        // 展平为一维，偏移后回填
        let flat: Vec<i32> = grid.into_iter().flatten().collect();
        let mut shifted = vec![0; total];

        for (i, &val) in flat.iter().enumerate() {
            shifted[(i + k) % total] = val;
        }

        // 重新组织为二维
        shifted
            .chunks(cols)
            .map(|chunk| chunk.to_vec())
            .collect()
    }
}
```
