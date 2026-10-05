---
title: "leetcode-位运算56"
date: 2026-09-24T10:33:37+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 最大异或乘积

给你三个整数 a ，b 和 n ，请你返回 (a XOR x) * (b XOR x) 的 最大值 且 x 需要满足 0 <= x < 2^n。

由于答案可能会很大，返回它对 10^9 + 7 取余 后的结果。

注意，XOR 是按位异或操作。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn maximum_xor_product(mut a: i64, mut b: i64, n: i32) -> i32 {
        // 只考虑低 n 位，高位不受 x 影响
        let mask: i64 = (1i64 << n) - 1;
        // c 中为 1 的位表示 a 和 b 在该位不同，需要分配
        let c = (a ^ b) & mask;

        // 对于 c 中为 0 的位，a 和 b 相同，我们可以通过选择 x 让它们都变为 1
        // 所以直接将 a 和 b 在这些位都置为 1（即或上 mask ^ c）
        a = (a & !mask) | (mask ^ c);
        b = (b & !mask) | (mask ^ c);

        // 现在 a 和 b 在低 n 位中，c 为 1 的位暂时为 0，c 为 0 的位已经为 1。
        // 接下来从高位到低位分配 c 中为 1 的位，每次给当前较小的数，
        // 使得两个数尽可能接近，从而乘积最大。
        for i in (0..n).rev() {
            if (c >> i) & 1 == 1 {
                let bit = 1i64 << i;
                if a < b {
                    a |= bit;
                } else {
                    b |= bit;
                }
            }
        }

        ((a % MOD) * (b % MOD) % MOD) as i32
    }
}
```
