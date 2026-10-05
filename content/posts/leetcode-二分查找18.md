---
title: "leetcode-二分查找18"
date: 2026-09-28T12:29:11+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 乘法表中第k小的数

几乎每一个人都用 乘法表。但是你能在乘法表中快速找到第 k 小的数字吗？

乘法表是大小为 m x n 的一个整数矩阵，其中 mat[i][j] == i * j（下标从 1 开始）。

给你三个整数 m、n 和 k，请你在大小为 m x n 的乘法表中，找出并返回第 k 小的数字。


```
impl Solution {
    pub fn find_kth_number(m: i32, n: i32, k: i32) -> i32 {
        // 二分答案：统计乘法表中 <= mid 的数字个数，找到第一个使 count >= k 的 mid
        let (mut left, mut right) = (1, m * n);
        while left < right {
            let mid = left + (right - left) / 2;
            let mut count = 0;
            // 第 i 行中 <= mid 的个数为 min(n, mid / i)
            for i in 1..=m {
                count += n.min(mid / i);
            }
            if count >= k {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        left
    }
}
```
