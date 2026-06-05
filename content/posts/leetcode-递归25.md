---
title: "leetcode-递归25"
date: 2026-05-26T10:00:18+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 好因子的最大数目


给你一个正整数 primeFactors 。你需要构造一个正整数 n ，它满足以下条件：

n 质因数（质因数需要考虑重复的情况）的数目 不超过 primeFactors 个。

n 好因子的数目最大化。如果 n 的一个因子可以被 n 的每一个质因数整除，我们称这个因子是 好因子 。比方说，如果 n = 12 ，那么它的质因数为 [2,2,3] ，那么 6 和 12 是好因子，但 3 和 4 不是。

请你返回 n 的好因子的数目。由于答案可能会很大，请返回答案对 10^9 + 7 取余 的结果。

请注意，一个质数的定义是大于 1 ，且不能被分解为两个小于该数的自然数相乘。一个数 n 的质因子是将 n 分解为若干个质因子，且它们的乘积为 n 。

```
const MOD: i64 = 1_000_000_007;

impl Solution {
    /// 找出最大好除数（优化版）
    ///
    /// # 数学原理
    /// 整数拆分问题：将 n 拆分成若干个正整数之和，使得乘积最大
    /// - 最优解：尽量拆分成 3
    /// - 特殊处理：余数为 1 时拆成 2+2（因为 3+1 < 2+2）
    /// - 余数为 2 时保留一个 2
    ///
    /// # 复杂度
    /// - 时间复杂度：O(log n) - 快速幂
    /// - 空间复杂度：O(1)
    ///
    /// # 示例
    /// ```
    /// assert_eq!(Solution::max_nice_divisors(5), 6);
    /// // 解释：5 = 2 + 3，乘积 = 6
    /// ```
    pub fn max_nice_divisors(prime_factors: i32) -> i32 {
        let n = prime_factors as i64;

        match n {
            0 | 1 => 1,
            2 => 2,
            3 => 3,
            _ => {
                let (q, r) = (n / 3, n % 3);

                let result = match r {
                    0 => Self::pow(3, q as i32),
                    1 => Self::pow(3, (q - 1) as i32) * 4 % MOD,
                    _ => Self::pow(3, q as i32) * 2 % MOD, // r == 2
                };

                result as i32
            }
        }
    }

    /// 快速幂（迭代版本）
    ///
    /// 使用二进制指数分解，时间复杂度 O(log n)
    #[inline]
    fn pow(mut base: i64, mut exp: i32) -> i64 {
        let mut result = 1;

        while exp > 0 {
            if exp & 1 == 1 {
                result = (result * base) % MOD;
            }
            base = (base * base) % MOD;
            exp >>= 1;
        }

        result
    }

    /// 递归版本快速幂（更简洁但略慢）
    #[allow(dead_code)]
    fn pow_recursive(base: i64, exp: i32) -> i64 {
        match exp {
            0 => 1,
            e if e % 2 == 0 => Self::pow_recursive((base * base) % MOD, e / 2),
            _ => (base * Self::pow_recursive(base, exp - 1)) % MOD,
        }
    }
}
```


```

// 测试用例
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_max_nice_divisors() {
        assert_eq!(Solution::max_nice_divisors(1), 1);
        assert_eq!(Solution::max_nice_divisors(2), 2);
        assert_eq!(Solution::max_nice_divisors(3), 3);
        assert_eq!(Solution::max_nice_divisors(4), 4);
        assert_eq!(Solution::max_nice_divisors(5), 6);
        assert_eq!(Solution::max_nice_divisors(6), 9);
        assert_eq!(Solution::max_nice_divisors(7), 12);
        assert_eq!(Solution::max_nice_divisors(8), 18);
        assert_eq!(Solution::max_nice_divisors(10), 36);
    }

    #[test]
    fn test_pow() {
        assert_eq!(Solution::pow(3, 0), 1);
        assert_eq!(Solution::pow(3, 1), 3);
        assert_eq!(Solution::pow(3, 2), 9);
        assert_eq!(Solution::pow(3, 10), 59049);
        assert_eq!(Solution::pow(2, 10), 1024);
    }
}
```
