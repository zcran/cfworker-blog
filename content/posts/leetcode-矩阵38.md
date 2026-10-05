---
title: "leetcode-矩阵38"
date: 2026-09-19T10:41:34+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 寻找峰值 II

一个 2D 网格中的 峰值 是指那些 严格大于 其相邻格子(上、下、左、右)的元素。

给你一个 从 0 开始编号 的 m x n 矩阵 mat ，其中任意两个相邻格子的值都 不相同 。找出 任意一个 峰值 mat[i][j] 并 返回其位置 [i,j] 。

你可以假设整个矩阵周边环绕着一圈值为 -1 的格子。

要求必须写出时间复杂度为 O(m log(n)) 或 O(n log(m)) 的算法


```
impl Solution {
    pub fn find_peak_grid(mat: Vec<Vec<i32>>) -> Vec<i32> {
        let rows = mat.len();

        // 在行区间 [top, bottom] 上做二分：
        // 取中间行 mid，找到该行最大值所在的列 j。
        // 若 mat[mid][j] > mat[mid+1][j]，说明峰值在 mid 及其上方；否则在下方。
        let mut top = 0;
        let mut bottom = rows - 1;
        while top < bottom {
            let mid = top + (bottom - top) / 2;
            // 找到 mid 行最大值所在的列
            let j = (0..mat[mid].len())
                .max_by_key(|&k| mat[mid][k])
                .unwrap();
            if mat[mid][j] > mat[mid + 1][j] {
                bottom = mid;
            } else {
                top = mid + 1;
            }
        }

        // 最终行中最大值所在列即为峰值列
        let j = (0..mat[top].len())
            .max_by_key(|&k| mat[top][k])
            .unwrap();
        vec![top as i32, j as i32]
    }
}
```
