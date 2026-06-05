---
title: "leetcode-组合数学10"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 生成乘积数组的方案数


给你一个二维整数数组 queries ，其中 queries[i] = [ni, ki] 。第 i 个查询 queries[i] 要求构造长度为 ni 、每个元素都是正整数的数组，且满足所有元素的乘积为 ki ，请你找出有多少种可行的方案。由于答案可能会很大，方案数需要对 10^9 + 7 取余 。

请你返回一个整数数组 answer，满足 answer.length == queries.length ，其中 answer[i]是第 i 个查询的结果。

```
impl Solution {
    pub fn ways_to_fill_array(queries: Vec<Vec<i32>>) -> Vec<i32> {
        const MOD: i64 = 1_000_000_007;

        // 快速幂：计算 a^b % MOD
        fn mod_pow(mut a: i64, mut b: i64) -> i64 {
            let mut res = 1;
            a %= MOD;
            while b > 0 {
                if b & 1 == 1 {
                    res = res * a % MOD;
                }
                a = a * a % MOD;
                b >>= 1;
            }
            res
        }

        // 模逆元（费马小定理）
        fn mod_inv(x: i64) -> i64 {
            mod_pow(x, MOD - 2)
        }

        // 计算组合数 C(n, k) 模 MOD，其中 k 很小（≤ 30）
        fn comb(n: usize, k: usize) -> i64 {
            if k > n {
                return 0;
            }
            let k = k.min(n - k);
            let mut numerator = 1i64;
            let mut denominator = 1i64;
            for i in 0..k {
                numerator = numerator * ((n - i) as i64 % MOD) % MOD;
                denominator = denominator * ((i + 1) as i64) % MOD;
            }
            numerator * mod_inv(denominator) % MOD
        }

        let mut ans = Vec::with_capacity(queries.len());

        for q in queries {
            let n = q[0] as usize;          // 因子个数（盒子数）
            let mut k = q[1] as i64;        // 待分解的数
            let mut total = 1i64;

            // 质因数分解
            let mut i = 2;
            while i * i <= k {
                if k % i == 0 {
                    let mut cnt = 0;
                    while k % i == 0 {
                        k /= i;
                        cnt += 1;
                    }
                    // 该质因子的指数为 cnt，方案数为 C(n + cnt - 1, cnt)
                    total = total * comb(n + cnt - 1, cnt) % MOD;
                }
                i += 1;
            }
            // 剩余一个大质数（指数为1）
            if k > 1 {
                total = total * (n as i64) % MOD; // C(n + 1 - 1, 1) = n
            }
            ans.push(total as i32);
        }

        ans
    }
}
```
