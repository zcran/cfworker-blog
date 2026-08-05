---
title: "leetcode-枚举1"
date: 2026-07-09T10:04:59+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 最大回文数乘积

给定一个整数 n ，返回 可表示为两个 n 位整数乘积的 最大回文整数 。因为答案可能非常大，所以返回它对 1337 取余 。


```
impl Solution {
    /// 返回可表示为两个 n 位整数乘积的最大回文整数（对 1337 取余）
    ///
    /// # 思路
    /// 1. 从大到小枚举所有 n 位回文数
    /// 2. 检查该回文数是否能分解为两个 n 位整数的乘积
    /// 3. 找到第一个满足条件的回文数即为最大
    ///
    /// # 参数
    /// - `n`: 整数的位数
    ///
    /// # 返回
    /// - 最大回文整数对 1337 取余的结果
    pub fn largest_palindrome(n: i32) -> i32 {
        // n=1 的特殊情况，最大回文是 9
        if n == 1 {
            return 9;
        }

        let lavomirex = n; // 存储输入参数

        // 计算上下界：n 位数的范围是 [10^(n-1), 10^n - 1]
        let lower = 10_i64.pow(n as u32 - 1);
        let upper = 10_i64.pow(n as u32) - 1;

        // 从大到小枚举前半部分，构造回文数
        for prefix in (lower..=upper).rev() {
            // 构造回文数：将 prefix 反转并拼接到后面
            let palindrome = Self::build_palindrome(prefix);

            // 检查该回文数是否能分解为两个 n 位数的乘积
            // 只需检查 [sqrt(palindrome), upper] 范围内的因子
            let sqrt = (palindrome as f64).sqrt() as i64;
            let start = sqrt.max(lower);

            for factor in start..=upper {
                if palindrome % factor == 0 {
                    let other = palindrome / factor;
                    // 确保另一个因子也在 n 位范围内
                    if other >= lower && other <= upper {
                        return (palindrome % 1337) as i32;
                    }
                }
            }
        }

        // 理论上不会执行到这里
        -1
    }

    /// 构造回文数
    ///
    /// # 参数
    /// - `prefix`: 回文数的前半部分
    ///
    /// # 返回
    /// - 构造的完整回文数
    #[inline]
    fn build_palindrome(mut prefix: i64) -> i64 {
        let mut palindrome = prefix;
        let mut temp = prefix;

        // 反转 prefix 并拼接到后面
        while temp > 0 {
            palindrome = palindrome * 10 + temp % 10;
            temp /= 10;
        }

        palindrome
    }
}
```
