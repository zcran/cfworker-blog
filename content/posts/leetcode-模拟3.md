---
title: "leetcode-模拟3"
date: 2026-08-08T11:31:08+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 螺旋矩阵 II

给你一个正整数 n ，生成一个包含 1 到 n2 所有元素，且元素按顺时针顺序螺旋排列的 n x n 正方形矩阵 matrix 。


```
impl Solution {
    pub fn generate_matrix(n: i32) -> Vec<Vec<i32>> {
        let n = n as usize;
        let mut matrix = vec![vec![0; n]; n];
        let mut num = 1;
        let (mut top, mut bottom) = (0, n as i32 - 1);
        let (mut left, mut right) = (0, n as i32 - 1);

        while top <= bottom && left <= right {
            // 向右：填充 top 行
            for col in left..=right {
                matrix[top as usize][col as usize] = num;
                num += 1;
            }
            top += 1;

            // 向下：填充 right 列
            for row in top..=bottom {
                matrix[row as usize][right as usize] = num;
                num += 1;
            }
            right -= 1;

            // 向左：填充 bottom 行
            if top <= bottom {
                for col in (left..=right).rev() {
                    matrix[bottom as usize][col as usize] = num;
                    num += 1;
                }
                bottom -= 1;
            }

            // 向上：填充 left 列
            if left <= right {
                for row in (top..=bottom).rev() {
                    matrix[row as usize][left as usize] = num;
                    num += 1;
                }
                left += 1;
            }
        }

        matrix
    }
}
```
