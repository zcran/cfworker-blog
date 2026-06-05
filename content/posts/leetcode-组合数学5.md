---
title: "leetcode-组合数学5"
date: 2026-05-24T09:54:12+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 有效的快递序列数目


给你 n 笔订单，每笔订单都需要快递服务。

计算所有有效的 取货 / 交付 可能的顺序，使 delivery(i) 总是在 pickup(i) 之后。

由于答案可能很大，请返回答案对 10^9 + 7 取余的结果。


```
impl Solution {
    pub fn count_orders(n: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = n as usize;
        let mut ans = 1_i64;

        // 递推公式：f(i) = f(i-1) * (2*i-1) * i
        for i in 2..=n {
            ans = ans * (2 * i as i64 - 1) % MOD;
            ans = ans * i as i64 % MOD;
        }

        ans as i32
    }
}
```
