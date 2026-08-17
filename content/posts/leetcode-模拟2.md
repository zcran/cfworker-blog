---
title: "leetcode-模拟2"
date: 2026-08-08T11:31:08+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 螺旋矩阵

给你一个 m 行 n 列的矩阵 matrix ，请按照 顺时针螺旋顺序 ，返回矩阵中的所有元素。


```
impl Solution {
    pub fn spiral_order(matrix: Vec<Vec<i32>>) -> Vec<i32> {
        if matrix.is_empty() || matrix[0].is_empty() {
            return vec![];
        }

        let (mut top, mut bottom) = (0, matrix.len() as i32 - 1);
        let (mut left, mut right) = (0, matrix[0].len() as i32 - 1);
        let mut ans = Vec::new();

        while top <= bottom && left <= right {
            // 向右：遍历 top 行
            for col in left..=right {
                ans.push(matrix[top as usize][col as usize]);
            }
            top += 1;

            // 向下：遍历 right 列
            for row in top..=bottom {
                ans.push(matrix[row as usize][right as usize]);
            }
            right -= 1;

            // 向左：遍历 bottom 行（需确认还有未遍历的行）
            if top <= bottom {
                for col in (left..=right).rev() {
                    ans.push(matrix[bottom as usize][col as usize]);
                }
                bottom -= 1;
            }

            // 向上：遍历 left 列（需确认还有未遍历的列）
            if left <= right {
                for row in (top..=bottom).rev() {
                    ans.push(matrix[row as usize][left as usize]);
                }
                left += 1;
            }
        }

        ans
    }
}
```
