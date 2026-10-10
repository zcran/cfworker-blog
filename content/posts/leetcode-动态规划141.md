---
title: "leetcode-动态规划141"
date: 2026-10-05T20:40:03+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 好子序列的元素之和

给你一个整数数组 nums。好子序列 的定义是：子序列中任意 两个 连续元素的绝对差 恰好 为 1。

子序列 是指可以通过删除某个数组的部分元素（或不删除）得到的数组，并且不改变剩余元素的顺序。

返回 nums 中所有 可能存在的 好子序列的 元素之和。

因为答案可能非常大，返回结果需要对 10^9 + 7 取余。

注意，长度为 1 的子序列默认为好子序列。


```
impl Solution {
    pub fn sum_of_good_subsequences(nums: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;

        let max_v = *nums.iter().max().unwrap() as usize;

        // 下标整体 +1，避免 x-1 时出现 -1
        // cnt[v]：以值 v 结尾的好子序列个数
        // sum[v]：以值 v 结尾的所有好子序列的元素之和
        let mut cnt = vec![0i64; max_v + 3];
        let mut sum = vec![0i64; max_v + 3];

        for &x in &nums {
            let x = x as usize;

            // 以 x 结尾的好子序列：可由 x-1、x+1 结尾的序列接上，或单独成段
            // 对应下标偏移：x -> x+1, x-1 -> x, x+1 -> x+2
            let c = (cnt[x] + cnt[x + 2] + 1) % MOD;

            // 新增元素之和 = x * c；再加上继承自 x-1 / x+1 的原有和
            sum[x + 1] = (x as i64 * c + sum[x] + sum[x + 1] + sum[x + 2]) % MOD;
            cnt[x + 1] = (cnt[x + 1] + c) % MOD;
        }

        sum.iter().fold(0i64, |acc, &v| (acc + v) % MOD) as i32
    }
}
```
