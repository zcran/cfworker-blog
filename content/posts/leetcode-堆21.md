---
title: "leetcode-堆21"
date: 2026-08-21T09:54:12+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 3n 块披萨

给你一个披萨，它由 3n 块不同大小的部分组成，现在你和你的朋友们需要按照如下规则来分披萨：

你挑选 任意 一块披萨。
Alice 将会挑选你所选择的披萨逆时针方向的下一块披萨。
Bob 将会挑选你所选择的披萨顺时针方向的下一块披萨。
重复上述过程直到没有披萨剩下。

每一块披萨的大小按顺时针方向由循环数组 slices 表示。

请你返回你可以获得的披萨大小总和的最大值。


```
impl Solution {
    /// 环形打家劫舍：从 3n 块中选 n 块不相邻的，最大和
    pub fn max_size_slices(slices: Vec<i32>) -> i32 {
        let n = slices.len();
        let k = n / 3; // 总共要选 n 块

        // 环形拆成两个线性问题：不选第一块，或不选最后一块
        Self::linear_max(&slices[1..], k).max(Self::linear_max(&slices[..n - 1], k))
    }

    /// 线性版本：从 m 块中选 k 块不相邻的，最大和
    fn linear_max(s: &[i32], k: usize) -> i32 {
        let m = s.len();
        let mut dp = vec![0; m + 2];     // 上一轮：前 i 块中选 j-1 块的最大和
        let mut new_dp = vec![0; m + 2]; // 当前轮：前 i 块中选 j 块的最大和

        for _ in 0..k {
            for i in 2..=m + 1 {
                // 不选第 i-2 块，或选第 i-2 块（则 i-3 块不能选）
                new_dp[i] = new_dp[i - 1].max(dp[i - 2] + s[i - 2]);
            }
            std::mem::swap(&mut dp, &mut new_dp);
        }

        dp[m + 1]
    }
}
```
