---
title: "leetcode-双指针113"
date: 2026-09-05T09:53:08+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 最少翻转次数使二进制矩阵回文 II

给你一个 m x n 的二进制矩阵 grid 。

如果矩阵中一行或者一列从前往后与从后往前读是一样的，那么我们称这一行或者这一列是 回文 的。

你可以将 grid 中任意格子的值 翻转 ，也就是将格子里的值从 0 变成 1 ，或者从 1 变成 0 。

请你返回 最少 翻转次数，使得矩阵中 所有 行和列都是 回文的 ，且矩阵中 1 的数目可以被 4 整除 。


```
impl Solution {
    /// 使矩阵所有行、所有列都回文，且 1 的总数能被 4 整除的最少翻转次数。
    ///
    /// 思路：按四元组分类处理。
    /// 1. 四个对称位置 (i,j),(i,n-1-j),(m-1-i,j),(m-1-i,n-1-j) 构成一组，
    ///    翻转成相同值的最优代价为 min(cnt1, 4-cnt1)，累加到 ans。
    /// 2. 处理中心行/中心列上成对对称但不构成四元组的位置：
    ///    若两点不同（diff），需 1 次翻转统一；
    ///    若相同且为 1（cnt1 += 2），统一后贡献偶数个 1。
    /// 3. 中心格（m、n 均为奇数时）翻转代价为自身值。
    /// 4. 若 diff > 0，可以用其中一次翻转来调整 1 的总数奇偶，额外代价即 diff；
    ///    否则只能靠翻转中心格或一组偶数 1 来修正，代价为 cnt1 % 4。
    ///
    /// 时间复杂度 O(m * n)，空间复杂度 O(1)。
    pub fn min_flips(grid: Vec<Vec<i32>>) -> i32 {
        let m = grid.len();
        let n = grid[0].len();
        let mut ans = 0;

        // 1. 四元组：四个对称位置翻成相同值
        for i in 0..m / 2 {
            for j in 0..n / 2 {
                let cnt = grid[i][j]
                    + grid[i][n - 1 - j]
                    + grid[m - 1 - i][j]
                    + grid[m - 1 - i][n - 1 - j];
                ans += cnt.min(4 - cnt);
            }
        }

        // 2. 中心行上成对的对称位置（不含中心格）
        let mut diff = 0;
        let mut cnt1 = 0;
        if m % 2 == 1 {
            let mid = m / 2;
            for j in 0..n / 2 {
                if grid[mid][j] != grid[mid][n - 1 - j] {
                    diff += 1;
                } else {
                    cnt1 += grid[mid][j] * 2;
                }
            }
        }

        // 3. 中心列上成对的对称位置（不含中心格）
        if n % 2 == 1 {
            let mid = n / 2;
            for i in 0..m / 2 {
                if grid[i][mid] != grid[m - 1 - i][mid] {
                    diff += 1;
                } else {
                    cnt1 += grid[i][mid] * 2;
                }
            }
        }

        // 4. 中心格（m、n 均为奇数）
        if m % 2 == 1 && n % 2 == 1 {
            ans += grid[m / 2][n / 2];
        }

        // 5. 修正 1 的总数使其能被 4 整除
        if diff > 0 {
            ans += diff;
        } else {
            ans += cnt1 % 4;
        }

        ans
    }
}
```
