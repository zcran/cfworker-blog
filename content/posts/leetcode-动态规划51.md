---
title: "leetcode-动态规划51"
date: 2026-10-05T20:39:57+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 最长的斐波那契子序列的长度

如果序列 x1, x2, ..., xn 满足下列条件，就说它是 斐波那契式 的：

n >= 3
对于所有 i + 2 <= n，都有 xi + xi+1 == xi+2

给定一个 严格递增 的正整数数组形成序列 arr ，找到 arr 中最长的斐波那契式的子序列的长度。如果不存在，返回  0 。

子序列 是通过从另一个序列 arr 中删除任意数量的元素（包括删除 0 个元素）得到的，同时不改变剩余元素顺序。例如，[3, 5, 8] 是 [3, 4, 5, 6, 7, 8] 的子序列。


```
impl Solution {
    /// 873. 最长的斐波那契式子序列
    ///
    /// dp[j][k] 表示以 arr[j]、arr[k] 作为最后两个元素的最长斐波那契式子序列长度。
    /// 对每个 k，用双指针 i、j 在 [0, k) 内寻找 arr[i] + arr[j] == arr[k]：
    ///   - 若和小于 arr[k]，i 右移；若和大于 arr[k]，j 左移；
    ///   - 相等时，dp[j][k] = dp[i][j] + 1。
    ///
    /// 由于 dp[i][j] 至少为 2（空序列 + 两个元素），最终答案是 max + 2（加上 i、j 两元素本身）。
    /// 若不存在任何长度 >= 3 的斐波那契子序列，返回 0。
    pub fn len_longest_fib_subseq(arr: Vec<i32>) -> i32 {
        let n = arr.len();
        let mut dp = vec![vec![0u16; n]; n]; // 长度上界有限，用 u16 省内存
        let mut best = 0u16;

        for k in 2..n {
            let (mut i, mut j) = (0, k - 1);
            while i < j {
                let sum = arr[i] + arr[j];
                if sum < arr[k] {
                    i += 1;
                } else if sum > arr[k] {
                    j -= 1;
                } else {
                    dp[j][k] = dp[i][j] + 1;
                    best = best.max(dp[j][k]);
                    i += 1;
                    j -= 1;
                }
            }
        }

        if best == 0 { 0 } else { best as i32 + 2 }
    }
}
```
