---
title: "leetcode-动态规划6"
date: 2026-10-05T20:39:54+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 杨辉三角

给定一个非负整数 numRows，生成「杨辉三角」的前 numRows 行。

在「杨辉三角」中，每个数是它左上方和右上方的数的和。


```
impl Solution {
    pub fn generate(num_rows: i32) -> Vec<Vec<i32>> {
        let n = num_rows as usize;
        let mut triangle: Vec<Vec<i32>> = Vec::with_capacity(n);

        for i in 0..n {
            // 每行先填满 1，再更新中间元素
            let mut row = vec![1i32; i + 1];
            for j in 1..i {
                // 左上方的数 + 正上方的数
                row[j] = triangle[i - 1][j - 1] + triangle[i - 1][j];
            }
            triangle.push(row);
        }

        triangle
    }
}
```
