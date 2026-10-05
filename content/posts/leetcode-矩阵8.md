---
title: "leetcode-矩阵8"
date: 2026-09-19T10:41:32+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 最大正方形

在一个由 '0' 和 '1' 组成的二维矩阵内，找到只包含 '1' 的最大正方形，并返回其面积。


```
impl Solution {
    pub fn maximal_square(matrix: Vec<Vec<char>>) -> i32 {
        if matrix.is_empty() || matrix[0].is_empty() {
            return 0;
        }

        let rows = matrix.len();
        let cols = matrix[0].len();

        // 动态规划：dp[j] 表示以当前行第 j 列为右下角的最大正方形边长。
        // 状态转移：若 matrix[i][j] == '1'，
        //   dp[j] = min(左, 上, 左上) + 1
        // 用一维滚动数组，prev 保存"左上"（上一行前一列）的值，空间 O(n)。
        let mut dp = vec![0i32; cols + 1];
        let mut max_side = 0;

        for i in 0..rows {
            let mut prev = 0; // 相当于 dp[i-1][j-1]，每行开始重置为 0
            for j in 0..cols {
                let temp = dp[j + 1]; // 保存当前 dp[j+1]（即"上"），下一列会作为"左上"使用
                if matrix[i][j] == '1' {
                    // 左 = dp[j+1] 更新前的 dp[j]（用 dp[j+1] 存储当前列，dp[j] 为左侧）
                    // 这里 dp[j+1] 表示上一行同列（上），dp[j] 表示当前行前一列（左）
                    dp[j + 1] = dp[j].min(dp[j + 1]).min(prev) + 1;
                    max_side = max_side.max(dp[j + 1]);
                } else {
                    dp[j + 1] = 0;
                }
                prev = temp;
            }
        }

        max_side * max_side
    }
}
```
