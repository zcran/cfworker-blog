---
title: "leetcode-数组46"
date: 2026-10-10T20:38:43+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 对角线最长的矩形的面积

给你一个下标从 0 开始的二维整数数组 dimensions。

对于所有下标 i（0 <= i < dimensions.length），dimensions[i][0] 表示矩形 i 的长度，而 dimensions[i][1] 表示矩形 i 的宽度。

返回对角线最 长 的矩形的 面积 。如果存在多个对角线长度相同的矩形，返回其中面积最 大 的矩形的面积。


```
impl Solution {
    /// 返回对角线最长的矩形面积；若对角线等长，返回面积更大的那个。
    ///
    /// 对角线长度的平方为 `l² + w²`，面积 `l * w`。
    /// 一次遍历，维护「最大对角线平方」及对应的最大面积。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn area_of_max_diagonal(dimensions: Vec<Vec<i32>>) -> i32 {
        let mut best_diag = 0;
        let mut best_area = 0;

        for dim in &dimensions {
            let (l, w) = (dim[0], dim[1]);
            let diag = l * l + w * w;
            let area = l * w;

            if diag > best_diag || (diag == best_diag && area > best_area) {
                best_diag = diag;
                best_area = area;
            }
        }

        best_area
    }
}
```
