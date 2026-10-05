---
title: "leetcode-贪心163"
date: 2026-10-03T12:55:45+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 最大交替平方和

给你一个整数数组 nums。你可以以任意顺序 重新排列元素 。

数组 arr 的 交替得分 定义为：

score = arr[0]^2 - arr[1]^2 + arr[2]^2 - arr[3]^2 + ...

在对 nums 重新排列后，返回其 最大可能的交替得分。


```
impl Solution {
    pub fn max_alternating_sum(mut nums: Vec<i32>) -> i64 {
        // 交替得分中正项系数为 +1、负项为 -1。
        // 为最大化结果，应让绝对值大的数取正号，绝对值小的数取负号。
        // 按绝对值升序排序后，前 floor(n/2) 个取负，其余取正。
        // 注意：n 为奇数时，正项比负项多一个，恰好让最大的元素多占一个正号。
        nums.sort_unstable_by_key(|&x| x.abs());

        let n = nums.len();
        let neg_cnt = n / 2;

        nums.iter()
            .enumerate()
            .map(|(i, &x)| {
                let sq = (x as i64) * (x as i64);
                if i < neg_cnt { -sq } else { sq }
            })
            .sum()
    }
}
```
