---
title: "leetcode-前缀和62"
date: 2026-08-25T10:02:49+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 转角路径的乘积中最多能有几个尾随零

给你一个二维整数数组 grid ，大小为 m x n，其中每个单元格都含一个正整数。

转角路径 定义为：包含至多一个弯的一组相邻单元。具体而言，路径应该完全 向水平方向 或者 向竖直方向 移动过弯（如果存在弯），而不能访问之前访问过的单元格。在过弯之后，路径应当完全朝 另一个 方向行进：如果之前是向水平方向，那么就应该变为向竖直方向；反之亦然。当然，同样不能访问之前已经访问过的单元格。

一条路径的 乘积 定义为：路径上所有值的乘积。

请你从 grid 中找出一条乘积中尾随零数目最多的转角路径，并返回该路径中尾随零的数目。

注意：

水平 移动是指向左或右移动。
竖直 移动是指向上或下移动。


```
impl Solution {
    pub fn max_trailing_zeros(grid: Vec<Vec<i32>>) -> i32 {
        let m = grid.len();
        let n = grid[0].len();

        // 计算每个数中因子2和因子5的个数
        // factor[i][j] = (count_2, count_5)
        let mut factor = vec![vec![(0, 0); n]; m];
        for i in 0..m {
            for j in 0..n {
                let mut x = grid[i][j];
                let mut cnt2 = 0;
                let mut cnt5 = 0;

                // 统计因子2的个数
                while x % 2 == 0 {
                    x /= 2;
                    cnt2 += 1;
                }
                // 统计因子5的个数
                while x % 5 == 0 {
                    x /= 5;
                    cnt5 += 1;
                }
                factor[i][j] = (cnt2, cnt5);
            }
        }

        // 行前缀和：pre_row[i][j] = 第i行前j列的因子之和
        let mut pre_row = vec![vec![(0, 0); n + 1]; m];
        for i in 0..m {
            for j in 1..=n {
                let prev = pre_row[i][j - 1];
                let curr = factor[i][j - 1];
                pre_row[i][j] = (prev.0 + curr.0, prev.1 + curr.1);
            }
        }

        // 列前缀和：pre_col[i][j] = 第j列前i行的因子之和
        let mut pre_col = vec![vec![(0, 0); n]; m + 1];
        for j in 0..n {
            for i in 1..=m {
                let prev = pre_col[i - 1][j];
                let curr = factor[i - 1][j];
                pre_col[i][j] = (prev.0 + curr.0, prev.1 + curr.1);
            }
        }

        let mut max_zeros = 0;

        // 枚举每个单元格作为拐点
        for i in 0..m {
            for j in 0..n {
                // 从四个方向到达拐点(i,j)的路径：
                // 1. 从左到拐点：行前缀 [0..j]
                let left = pre_row[i][j + 1];
                // 2. 从右到拐点：行后缀 [j..n-1]
                let right = (
                    pre_row[i][n].0 - pre_row[i][j].0,
                    pre_row[i][n].1 - pre_row[i][j].1,
                );
                // 3. 从上到拐点：列前缀 [0..i]
                let up = pre_col[i + 1][j];
                // 4. 从下到拐点：列后缀 [i..m-1]
                let down = (
                    pre_col[m][j].0 - pre_col[i][j].0,
                    pre_col[m][j].1 - pre_col[i][j].1,
                );

                // 拐点本身（会被重复计算，需要减去一次）
                let center = factor[i][j];

                // 四种转角路径：
                // 左+上、左+下、右+上、右+下
                let paths = [
                    (left.0 + up.0 - center.0, left.1 + up.1 - center.1),
                    (left.0 + down.0 - center.0, left.1 + down.1 - center.1),
                    (right.0 + up.0 - center.0, right.1 + up.1 - center.1),
                    (right.0 + down.0 - center.0, right.1 + down.1 - center.1),
                ];

                for (cnt2, cnt5) in paths {
                    // 尾随零的个数 = min(因子2总数, 因子5总数)
                    max_zeros = max_zeros.max(cnt2.min(cnt5));
                }
            }
        }

        max_zeros
    }
}
```
