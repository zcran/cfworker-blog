---
title: "leetcode-矩阵23"
date: 2026-09-19T10:41:33+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 统计全为 1 的正方形子矩阵

给你一个 m * n 的矩阵，矩阵中的元素不是 0 就是 1，请你统计并返回其中完全由 1 组成的 正方形 子矩阵的个数。


```
impl Solution {
    pub fn count_squares(matrix: Vec<Vec<i32>>) -> i32 {
        let m = matrix.len();
        let n = matrix[0].len();
        // f[j] 表示以当前行第 j 列为右下角的最大全 1 正方形边长
        // 使用一维数组滚动更新，省去 m 行的额外内存
        let mut f = vec![0i32; n];
        let mut ans = 0;

        for i in 0..m {
            // prev 保存上一行第 j 列左侧（即 f[i-1][j-1]）的值
            let mut prev = 0;
            for j in 0..n {
                let temp = f[j]; // 记录当前 f[j]，作为下一列的 prev
                if matrix[i][j] == 1 {
                    if i == 0 || j == 0 {
                        f[j] = 1;
                    } else {
                        // f[j] 此时对应 f[i-1][j]，prev 对应 f[i-1][j-1]，f[j-1] 对应 f[i][j-1]
                        f[j] = f[j].min(f[j - 1]).min(prev) + 1;
                    }
                } else {
                    f[j] = 0;
                }
                ans += f[j];
                prev = temp;
            }
        }

        ans
    }
}
```
