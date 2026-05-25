---
title: "leetcode-位掩码27"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 无平方子集计数

给你一个正整数数组 nums 。

如果数组 nums 的子集中的元素乘积是一个 无平方因子数 ，则认为该子集是一个 无平方 子集。

无平方因子数 是无法被除 1 之外任何平方数整除的数字。

返回数组 nums 中 无平方 且 非空 的子集数目。因为答案可能很大，返回对 109 + 7 取余的结果。

nums 的 非空子集 是可以由删除 nums 中一些元素（可以不删除，但不能全部删除）得到的一个数组。如果构成两个子集时选择删除的下标不同，则认为这两个子集不同。


```
impl Solution {
    /// 统计无平方因子子集的数量（LeetCode 2572）
    pub fn square_free_subsets(nums: Vec<i32>) -> i32 {
        const PRIMES: [i32; 10] = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29];
        const MOD: i64 = 1_000_000_007;
        const MX: usize = 30;
        const M: usize = 1 << PRIMES.len(); // 1024

        // 1. 预计算每个数字（1..30）的质因子掩码
        let mut mask_of = [0i32; MX + 1]; // 0 表示无质因子（对应数字 1）
        for i in 2..=MX {
            let mut m = 0;
            let mut ok = true;
            for (j, &p) in PRIMES.iter().enumerate() {
                if i as i32 % p == 0 {
                    if i as i32 % (p * p) == 0 {
                        ok = false; // 有平方因子
                        break;
                    }
                    m |= 1 << j;
                }
            }
            mask_of[i] = if ok { m } else { -1 };
        }

        // 2. 动态规划：f[mask] = 方案数（mask 是质因子集合）
        let mut f = [0i64; M];
        f[0] = 1; // 空集方案数为 1

        for &x in &nums {
            let x = x as usize;
            if x > MX { continue; } // 防御，原题数字范围 1..30
            let mask = mask_of[x];
            if mask < 0 { continue; } // 跳过含平方因子的数字
            let mask = mask as usize;

            // 逆序更新，确保每个数字只使用一次
            for j in (mask..M).rev() {
                if (j | mask) == j { // mask 是 j 的子集
                    f[j] = (f[j] + f[j ^ mask]) % MOD;
                }
            }
        }

        // 3. 结果：所有非空子集方案数之和（去掉空集）
        let total = f.iter().sum::<i64>() - 1;
        (total % MOD) as i32
    }
}
```
