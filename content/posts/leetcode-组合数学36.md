---
title: "leetcode-组合数学36"
date: 2026-05-24T09:54:14+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 统计恰好有 K 个相等相邻元素的数组数目

给你三个整数 n ，m ，k 。长度为 n 的 好数组 arr 定义如下：

arr 中每个元素都在 闭 区间 [1, m] 中。
恰好 有 k 个下标 i （其中 1 <= i < n）满足 arr[i - 1] == arr[i] 。

请你返回可以构造出的 好数组 数目。

由于答案可能会很大，请你将它对 10^9 + 7 取余 后返回。

```
const MOD: i64 = 1_000_000_007;
const MX: usize = 100_000;  // 根据题目 n 的最大范围设定

// 全局静态数组（使用 unsafe 但确保单线程安全）
static mut FACT: [i64; MX] = [0; MX];
static mut INV_FACT: [i64; MX] = [0; MX];

// 快速幂
fn mod_pow(mut x: i64, mut n: i32) -> i64 {
    let mut res = 1;
    while n > 0 {
        if n & 1 == 1 {
            res = res * x % MOD;
        }
        x = x * x % MOD;
        n >>= 1;
    }
    res
}

// 懒初始化：只执行一次
fn init() {
    unsafe {
        if FACT[0] != 0 {
            return; // 已初始化
        }
        FACT[0] = 1;
        for i in 1..MX {
            FACT[i] = FACT[i - 1] * (i as i64) % MOD;
        }
        INV_FACT[MX - 1] = mod_pow(FACT[MX - 1], (MOD - 2) as i32);
        for i in (1..MX).rev() {
            INV_FACT[i - 1] = INV_FACT[i] * (i as i64) % MOD;
        }
    }
}

// 组合数 C(n, m)
fn comb(n: usize, m: usize) -> i64 {
    unsafe {
        FACT[n] * INV_FACT[m] % MOD * INV_FACT[n - m] % MOD
    }
}

impl Solution {
    pub fn count_good_arrays(n: i32, m: i32, k: i32) -> i32 {
        init();
        let n = n as usize;
        let k = k as usize;
        // 公式：第一个数 m 种选择，在 n-1 个间隔中选 k 个作为“相等”，其余间隔各 (m-1) 种选择
        (comb(n - 1, k) * (m as i64 % MOD) % MOD * mod_pow((m - 1) as i64, (n - k - 1) as i32) % MOD) as i32
    }
}
```
