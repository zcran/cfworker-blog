---
title: "leetcode-模拟10"
date: 2026-08-08T11:31:08+08:00
tags: ["leetcode", "模拟"]
draft: false
---


##  对角线遍历

给你一个大小为 m x n 的矩阵 mat ，请以对角线遍历的顺序，用一个数组返回这个矩阵中的所有元素。


```
impl Solution {
    pub fn find_diagonal_order(mat: Vec<Vec<i32>>) -> Vec<i32> {
        let m = mat.len();
        let n = mat[0].len();
        let mut ans = Vec::with_capacity(m * n);

        for k in 0..m + n - 1 {
            let start = k.saturating_sub(m - 1);
            let end = k.min(n - 1);

            if k % 2 == 0 {
                // 偶数对角线：从下往上，j 从小到大，i = k - j 从大到小
                for j in start..=end {
                    ans.push(mat[k - j][j]);
                }
            } else {
                // 奇数对角线：从上往下，j 从大到小，i = k - j 从小到大
                for j in (start..=end).rev() {
                    ans.push(mat[k - j][j]);
                }
            }
        }

        ans
    }
}
```
