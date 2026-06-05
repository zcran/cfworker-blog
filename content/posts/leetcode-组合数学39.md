---
title: "leetcode-组合数学39"
date: 2026-05-24T09:54:14+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 判断操作后字符串中的数字是否相等 I

给你一个由数字组成的字符串 s 。重复执行以下操作，直到字符串恰好包含 两个 数字：

从第一个数字开始，对于 s 中的每一对连续数字，计算这两个数字的和 模 10。
用计算得到的新数字依次替换 s 的每一个字符，并保持原本的顺序。

如果 s 最后剩下的两个数字 相同 ，返回 true 。否则，返回 false。


```
impl Solution {
    pub fn has_same_digits(s: String) -> bool {
        // 将字符串转为数字数组，避免后续重复做 ASCII 转换
        let digits: Vec<u8> = s.bytes().map(|b| b - b'0').collect();
        let n = digits.len();

        // 长度为 2 时无需操作，直接比较
        if n == 2 {
            return digits[0] == digits[1];
        }

        // 共需进行 m = n - 2 轮相邻求和，最终剩下的两个数字分别等于：
        // res0 = Σ_{k=0..m} C(m, k) * digits[k]       mod 10
        // res1 = Σ_{k=0..m} C(m, k) * digits[k + 1]   mod 10
        // 因此问题转化为计算这两个模 10 的加权和，并比较是否相等。
        let m = n - 2;

        // ---------- 1. 预处理：用于快速计算 C(m, k) mod 5 ----------
        // 对于 x ∈ [0, m]，预存除去因子 5 后的余数（模 5）以及因子 5 的个数
        let mut v5 = vec![1u8; m + 1];   // v5[x] = x 去掉所有因子 5 后 mod 5
        let mut e5 = vec![0u8; m + 1];   // e5[x] = x 中因子 5 的个数
        for x in 2..=m {
            let mut temp = x;
            let mut e = 0;
            while temp % 5 == 0 {
                temp /= 5;
                e += 1;
            }
            e5[x] = e;
            v5[x] = (temp % 5) as u8;
        }

        // 模 5 的逆元表（0 未使用）
        let inv_mod5: [u8; 5] = [0, 1, 3, 2, 4]; // 1*1=1, 2*3=6≡1, 3*2=6≡1, 4*4=16≡1

        // ---------- 2. 递推计算 C(m, k) mod 2 与 mod 5，同时累加结果 ----------
        // 用于合并模 2 和模 5 的 CRT 常量：x = (5 * a + 6 * b) % 10, 其中 x ≡ a (mod 2), x ≡ b (mod 5)
        let mut c_mod5 = 1u8;   // C(m, k) mod 5 的非零部分
        let mut c_e5 = 0u8;     // C(m, k) 中因子 5 的个数

        let (mut sum0_mod2, mut sum0_mod5) = (0u8, 0u8);
        let (mut sum1_mod2, mut sum1_mod5) = (0u8, 0u8);

        for k in 0..=m {
            // 模 2 的组合数：C(m,k) 为奇数当且仅当 k & (m - k) == 0
            let c_mod2 = if (k & (m - k)) == 0 { 1u8 } else { 0u8 };
            // 模 5 的组合数：若含有因子 5 则为 0，否则为当前余数
            let c_mod5_val = if c_e5 > 0 { 0u8 } else { c_mod5 };

            let a_k = digits[k];
            let a_k1 = digits[k + 1];

            // 累加至两个目标数字的模 2 和模 5 分量
            sum0_mod2 = (sum0_mod2 + c_mod2 * (a_k & 1)) % 2;
            sum0_mod5 = (sum0_mod5 + c_mod5_val * (a_k % 5)) % 5;
            sum1_mod2 = (sum1_mod2 + c_mod2 * (a_k1 & 1)) % 2;
            sum1_mod5 = (sum1_mod5 + c_mod5_val * (a_k1 % 5)) % 5;

            // 由 C(m, k) 递推 C(m, k+1) = C(m, k) * (m - k) / (k + 1)
            if k < m {
                let num = m - k;
                let den = k + 1;
                // 乘分子
                c_e5 += e5[num];
                c_mod5 = (c_mod5 * v5[num]) % 5;
                // 除分母
                c_e5 -= e5[den];
                c_mod5 = (c_mod5 * inv_mod5[v5[den] as usize]) % 5;
            }
        }

        // ---------- 3. 用中国剩余定理合并模 10 ----------
        let res0 = (5 * sum0_mod2 + 6 * sum0_mod5) % 10;
        let res1 = (5 * sum1_mod2 + 6 * sum1_mod5) % 10;
        res0 == res1
    }
}
```
