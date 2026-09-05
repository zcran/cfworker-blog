---
title: "leetcode-前缀和27"
date: 2026-08-25T10:02:47+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 和为奇数的子数组数目

给你一个整数数组 arr 。请你返回和为 奇数 的子数组数目。

由于答案可能会很大，请你将结果对 10^9 + 7 取余后返回。


```
impl Solution {
    pub fn num_of_subarrays(arr: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;

        // odd: 以当前位置结尾的和为奇数的子数组个数
        // even: 以当前位置结尾的和为偶数的子数组个数
        let mut odd = 0;
        let mut even = 0;
        let mut ans = 0;

        for &x in &arr {
            if x % 2 == 0 {
                // 当前元素为偶数：奇偶性不变
                even += 1;
                // odd 保持不变
            } else {
                // 当前元素为奇数：奇偶性翻转
                // 新的 odd = 旧的 even + 1（当前元素自身）
                // 新的 even = 旧的 odd
                let new_odd = even + 1;
                even = odd;
                odd = new_odd;
            }

            ans = (ans + odd) % MOD;
        }

        ans as i32
    }
}
```
