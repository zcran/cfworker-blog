---
title: "leetcode-数学104"
date: 2026-10-07T20:39:35+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 解码值之和

给你一个整数数组 nums。

每个 nums[i] 都是一个 编码后的 整数，表示两个正整数 xi 和 yi。要解码 nums[i]，定义：

widthi = nums[i] % 10。
di = floor(nums[i] / 10)。
xi 为由 di 的十进制表示中前 widthi 位数字组成的整数。
yi 为由 di 的十进制表示中剩余所有数字组成的整数。
保证 di 的十进制表示包含的数字位数大于 widthi。因此，xi 和 yi 都至少包含一位数字。

nums[i] 的 解码值 为 xi^(yi)。

返回 nums 中所有元素的解码值之和，并对 10^9 + 7 取模。

floor() 函数返回除法结果的整数部分。


```
impl Solution {
    pub fn sum_decoded(nums: Vec<i64>) -> i32 {
        const MOD: i64 = 1_000_000_007;

        // 快速幂：(x^n) % MOD
        fn qpow(mut x: i64, mut n: i64) -> i64 {
            let mut res = 1;
            x %= MOD;
            while n > 0 {
                if n & 1 == 1 {
                    res = res * x % MOD;
                }
                x = x * x % MOD;
                n >>= 1;
            }
            res
        }

        let mut ans = 0i64;
        for x in nums {
            let d = x / 10;
            let width = (x % 10) as u32;

            // 计算 d 的十进制位数
            let length_d = {
                let mut len = 0u32;
                let mut v = d;
                while v > 0 {
                    len += 1;
                    v /= 10;
                }
                len
            };

            // 用 10 的幂拆分 d 为前缀 xi 和后缀 yi
            let pow10 = 10i64.pow(length_d - width);
            let xi = d / pow10;
            let yi = d % pow10;

            ans = (ans + qpow(xi, yi)) % MOD;
        }

        ans as i32
    }
}
```
