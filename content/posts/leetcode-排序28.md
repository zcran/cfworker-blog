---
title: "leetcode-排序28"
date: 2026-10-05T00:35:01+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 跳跃游戏 V

给你一个整数数组 arr 和一个整数 d 。每一步你可以从下标 i 跳到：

i + x ，其中 i + x < arr.length 且 0 < x <= d 。
i - x ，其中 i - x >= 0 且 0 < x <= d 。

除此以外，你从下标 i 跳到下标 j 需要满足：arr[i] > arr[j] 且 arr[i] > arr[k] ，其中下标 k 是所有 i 到 j 之间的数字（更正式的，min(i, j) < k < max(i, j)）。

你可以选择数组的任意下标开始跳跃。请你返回你 最多 可以访问多少个下标。

请注意，任何时刻你都不能跳到数组的外面。


```
impl Solution {
    /// 跳跃游戏中最多可以访问的下标数
    ///
    /// 规则：从 i 可跳到 j（|i-j| ≤ d），需满足 arr[i] > arr[j] 且沿途所有元素都小于 arr[i]。
    /// 思路：记忆化 DFS。f[i] 表示从 i 出发能访问的最大下标数。
    /// 由高度递减约束，该图无环，DFS 中递归求两端可达点即可。
    /// 时间 O(n·d)，额外空间 O(n)。
    pub fn max_jumps(arr: Vec<i32>, d: i32) -> i32 {
        let n = arr.len();
        let d = d as usize;
        let mut f = vec![0i32; n]; // 0 表示未计算

        fn dfs(arr: &[i32], f: &mut [i32], i: usize, d: usize) -> i32 {
            if f[i] != 0 {
                return f[i];
            }

            let mut best = 1;

            // 向左跳跃：距离 ≤ d，且沿途元素都小于 arr[i]
            let mut j = i;
            while j > 0 && i - j < d && arr[j - 1] < arr[i] {
                j -= 1;
                best = best.max(dfs(arr, f, j, d) + 1);
            }

            // 向右跳跃：距离 ≤ d，且沿途元素都小于 arr[i]
            let mut j = i + 1;
            while j < arr.len() && j - i <= d && arr[j] < arr[i] {
                best = best.max(dfs(arr, f, j, d) + 1);
                j += 1;
            }

            f[i] = best;
            best
        }

        (0..n).map(|i| dfs(&arr, &mut f, i, d)).max().unwrap()
    }
}
```
