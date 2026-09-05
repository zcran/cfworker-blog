---
title: "leetcode-前缀和26"
date: 2026-08-25T10:02:47+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 子数组和排序后的区间和

给你一个数组 nums ，它包含 n 个正整数。你需要计算所有非空连续子数组的和，并将它们按升序排序，得到一个新的包含 n * (n + 1) / 2 个数字的数组。

请你返回在新数组中下标为 left 到 right （下标从 1 开始）的所有数字和（包括左右端点）。由于答案可能很大，请你将它对 10^9 + 7 取模后返回。


```
impl Solution {
    pub fn range_sum(nums: Vec<i32>, n: i32, left: i32, right: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = n as usize;

        // 计算前缀和
        let mut prefix = vec![0; n + 1];
        for i in 0..n {
            prefix[i + 1] = prefix[i] + nums[i] as i64;
        }

        // 计算所有子数组和并排序（只保留前 right 个）
        let mut sums = Vec::with_capacity(right as usize);
        for i in 0..n {
            for j in i + 1..=n {
                sums.push(prefix[j] - prefix[i]);
            }
        }
        sums.sort_unstable();

        // 返回 left..=right 的和
        let mut ans = 0;
        for i in (left - 1) as usize..right as usize {
            ans = (ans + sums[i]) % MOD;
        }
        ans as i32
    }
}
```
