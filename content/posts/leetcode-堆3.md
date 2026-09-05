---
title: "leetcode-堆3"
date: 2026-08-21T09:54:10+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 有序矩阵中第 K 小的元素

给你一个 n x n 矩阵 matrix ，其中每行和每列元素均按升序排序，找到矩阵中第 k 小的元素。
请注意，它是 排序后 的第 k 小元素，而不是第 k 个 不同 的元素。

你必须找到一个内存复杂度优于 O(n^2) 的解决方案。


```
impl Solution {
    /// 二分查找求 n×n 有序矩阵中第 k 小的元素
    ///
    /// 矩阵每行每列均升序排列。
    /// 核心思想：对值域做二分，利用"从右上角往左下角走"统计 <= mid 的元素个数。
    /// 若个数 >= k，说明第 k 小元素 <= mid，收缩右边界；否则收缩左边界。
    pub fn kth_smallest(matrix: Vec<Vec<i32>>, k: i32) -> i32 {
        let n = matrix.len();
        let k = k as usize;

        // 统计矩阵中 <= limit 的元素个数（从右上角往左下角走）
        let count_le = |limit: i32| -> usize {
            let mut cnt = 0;
            let mut i = 0;
            let mut j = n - 1;
            while i < n {
                if matrix[i][j] > limit {
                    if j == 0 { break; }
                    j -= 1;
                } else {
                    cnt += j + 1;
                    i += 1;
                }
            }
            cnt
        };

        let mut left = matrix[0][0];
        let mut right = matrix[n - 1][n - 1];

        while left < right {
            let mid = left + (right - left) / 2;
            if count_le(mid) >= k {
                right = mid;
            } else {
                left = mid + 1;
            }
        }

        left
    }
}
```
