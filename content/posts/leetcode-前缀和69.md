---
title: "leetcode-前缀和69"
date: 2026-08-25T10:02:50+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 沙漏的最大总和

给你一个大小为 m x n 的整数矩阵 grid 。

按以下形式将矩阵的一部分定义为一个 沙漏 ：
![](../pic/img.jpg)

返回沙漏中元素的 最大 总和。

注意：沙漏无法旋转且必须整个包含在矩阵中。



```
impl Solution {
    pub fn max_sum(grid: Vec<Vec<i32>>) -> i32 {
        let rows = grid.len();
        let cols = grid[0].len();

        // 如果矩阵太小，直接返回0（虽然题目保证至少3x3）
        if rows < 3 || cols < 3 {
            return 0;
        }

        let mut max_sum = i32::MIN;

        // 遍历所有可能的沙漏左上角位置
        for i in 0..rows - 2 {
            for j in 0..cols - 2 {
                // 计算沙漏形状的和：
                // 第一行：3个元素
                // 第二行：中间1个元素
                // 第三行：3个元素
                let sum = grid[i][j] + grid[i][j + 1] + grid[i][j + 2]
                    + grid[i + 1][j + 1]
                    + grid[i + 2][j] + grid[i + 2][j + 1] + grid[i + 2][j + 2];

                max_sum = max_sum.max(sum);
            }
        }

        max_sum
    }
}
```
