---
title: "leetcode-前缀和138"
date: 2026-08-25T10:02:54+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 矩阵中的局部最大值 II

给你一个 n x m 的整数矩阵 matrix ，所有元素均为非负整数。

一个 非零 单元格 (row, col) 会按如下方式检查其附近的单元格：

· 令 x = matrix[row][col] 。
· 考虑在 (row, col) 的 x 行和 x 列范围内的每个单元格。
· 忽略矩阵外的单元格。
· 忽略行距离和列距离都恰好等于 x 的 单元格。

如果单元格 (row, col) 是 非零 的，并且所有考虑的单元格中没有一个值 大于 x ，那么该单元格就是一个 局部最大值 。

返回一个整数，表示 matrix 中 局部最大值 的数量。


```
impl Solution {
    pub fn count_local_maximums(matrix: Vec<Vec<i32>>) -> i32 {
        let n = matrix.len();
        let m = matrix[0].len();

        // 二维 ST 表：st[k1][k2][i][j] 表示从 (i,j) 开始，高度 2^k1，宽度 2^k2 的子矩阵最大值
        let wn = ((n as f64).log2().floor() as usize) + 1;
        let wm = ((m as f64).log2().floor() as usize) + 1;

        let mut st = vec![vec![vec![vec![0; m]; n]; wm]; wn];

        // 初始化单点
        for i in 0..n {
            for j in 0..m {
                st[0][0][i][j] = matrix[i][j];
            }
        }

        // 扩展列方向 (k1 = 0)
        for k2 in 1..wm {
            let half = 1 << (k2 - 1);
            for i in 0..n {
                for j in 0..=m - (1 << k2) {
                    st[0][k2][i][j] = st[0][k2 - 1][i][j].max(st[0][k2 - 1][i][j + half]);
                }
            }
        }

        // 扩展行方向
        for k1 in 1..wn {
            let half = 1 << (k1 - 1);
            for k2 in 0..wm {
                for i in 0..=n - (1 << k1) {
                    for j in 0..=m - (1 << k2) {
                        st[k1][k2][i][j] = st[k1 - 1][k2][i][j].max(st[k1 - 1][k2][i + half][j]);
                    }
                }
            }
        }

        // 查询子矩阵 [r1..r2) x [c1..c2) 的最大值
        let query = |r1: usize, c1: usize, r2: usize, c2: usize| -> i32 {
            if r1 >= r2 || c1 >= c2 {
                return i32::MIN;
            }
            let k1 = ((r2 - r1) as f64).log2().floor() as usize;
            let k2 = ((c2 - c1) as f64).log2().floor() as usize;
            let row_len = 1 << k1;
            let col_len = 1 << k2;

            let a = st[k1][k2][r1][c1];
            let b = st[k1][k2][r2 - row_len][c1];
            let c = st[k1][k2][r1][c2 - col_len];
            let d = st[k1][k2][r2 - row_len][c2 - col_len];
            a.max(b).max(c).max(d)
        };

        let mut ans = 0;
        for i in 0..n {
            for j in 0..m {
                let x = matrix[i][j];
                if x == 0 {
                    continue;
                }
                let x = x as usize;

                // 区域1：行范围 [i-x, i+x+1)，列范围 [j-x+1, j+x)
                let r1_1 = i.saturating_sub(x);
                let c1_1 = j.saturating_sub(x - 1); // x>=1
                let r2_1 = (i + x + 1).min(n);
                let c2_1 = (j + x).min(m);
                let max1 = query(r1_1, c1_1, r2_1, c2_1);

                // 区域2：行范围 [i-x+1, i+x)，列范围 [j-x, j+x+1)
                let r1_2 = i.saturating_sub(x - 1);
                let c1_2 = j.saturating_sub(x);
                let r2_2 = (i + x).min(n);
                let c2_2 = (j + x + 1).min(m);
                let max2 = query(r1_2, c1_2, r2_2, c2_2);

                if max1.max(max2) <= matrix[i][j] {
                    ans += 1;
                }
            }
        }

        ans
    }
}
```
