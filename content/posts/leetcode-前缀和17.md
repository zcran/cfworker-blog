---
title: "leetcode-前缀和17"
date: 2026-08-25T10:02:47+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 元素和小于等于阈值的正方形的最大边长

给你一个大小为 m x n 的矩阵 mat 和一个整数阈值 threshold。

请你返回元素总和小于或等于阈值的正方形区域的最大边长；如果没有这样的正方形区域，则返回 0 。


```
impl Solution {
    pub fn max_side_length(mat: Vec<Vec<i32>>, threshold: i32) -> i32 {
        let m = mat.len();
        let n = mat[0].len();
        let limit = m.min(n);

        // 二维前缀和，prefix[i+1][j+1] 表示 mat[0..=i][0..=j] 的和
        let mut prefix = vec![vec![0; n + 1]; m + 1];
        for i in 0..m {
            let mut row_sum = 0;
            for j in 0..n {
                row_sum += mat[i][j];
                prefix[i + 1][j + 1] = prefix[i][j + 1] + row_sum;
            }
        }

        // 判断是否存在边长为 size 的正方形满足条件
        let exists = |size: usize| -> bool {
            let end_i = m - size + 1;
            let end_j = n - size + 1;
            for i in 0..end_i {
                for j in 0..end_j {
                    let sum = prefix[i + size][j + size]
                        - prefix[i][j + size]
                        - prefix[i + size][j]
                        + prefix[i][j];
                    if sum <= threshold {
                        return true;
                    }
                }
            }
            false
        };

        // 二分搜索最大边长
        let mut left = 1;
        let mut right = limit;
        let mut ans = 0;

        while left <= right {
            let mid = (left + right) / 2;
            if exists(mid) {
                ans = mid as i32;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }

        ans
    }
}
```
