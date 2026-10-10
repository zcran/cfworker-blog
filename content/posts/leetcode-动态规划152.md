---
title: "leetcode-动态规划152"
date: 2026-10-05T20:40:04+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 统计逐位非递减的整数

给你两个以字符串形式表示的整数 l 和 r，以及一个整数 b。返回在区间 [l, r] （闭区间）内，以 b 进制表示时，其每一位数字为 非递减 顺序的整数个数。

整数逐位 非递减 需要满足：当按从左到右（从最高有效位到最低有效位）读取时，每一位数字都大于或等于前一位数字。

由于答案可能非常大，请返回对 10^9 + 7 取余 后的结果。


```
use std::cmp::min;

const MAX_N: usize = 333; // 进制转换后的最大长度
const MAX_B: usize = 10;
const MOD: i64 = 1_000_000_007;

/// 预处理组合数 comb[i][j] = C(i, j)，本题组合数较小，无需取模
static COMB: std::sync::LazyLock<[[i64; MAX_B]; MAX_N + MAX_B]> = std::sync::LazyLock::new(|| {
    let mut comb = [[0i64; MAX_B]; MAX_N + MAX_B];
    for i in 0..MAX_N + MAX_B {
        comb[i][0] = 1;
        for j in 1..min(i + 1, MAX_B) {
            comb[i][j] = comb[i - 1][j - 1] + comb[i - 1][j];
        }
    }
    comb
});

/// 把十进制字符串 `s` 转成 `b` 进制（低位在前）
/// 用竖式除法逐次取余。
fn trans(mut s: Vec<u8>, b: i32) -> Vec<i32> {
    // s 中是数字字符的 ASCII 值
    let mut digits: Vec<i32> = Vec::new();
    while !s.is_empty() {
        let mut nxt: Vec<u8> = Vec::with_capacity(s.len());
        let mut rem: i32 = 0;
        for &c in &s {
            rem = rem * 10 + (c as i32);
            let q = rem / b;
            if q != 0 || !nxt.is_empty() {
                nxt.push(q as u8);
            }
            rem %= b;
        }
        digits.push(rem);
        s = nxt;
    }
    digits.reverse(); // 高位在前
    digits
}

/// 判断数位是否非递减
fn is_non_dec(digits: &[i32]) -> bool {
    digits.windows(2).all(|w| w[0] <= w[1])
}

/// 计算 b 进制下严格小于 `s` 的合法数字个数；`check_s` 为真时额外判断 `s` 本身
fn calc(s: &str, b: i32, check_s: bool) -> i64 {
    let s_bytes: Vec<u8> = s.bytes().map(|c| c - b'0').collect();
    let digits = trans(s_bytes, b);
    let b = b as usize;

    let mut res: i64 = 0;
    let mut pre: usize = 0; // 前一位数字（初始为 0，因为最高位至少是 0）

    for (i, &d) in digits.iter().enumerate() {
        let hi = d as usize;
        if hi < pre {
            break; // 当前位比前一位小，已不满足非递减，后续也无法补救
        }
        let m = digits.len() - 1 - i;
        // 不受约束的方案数：C(m + b - pre, b - 1 - pre) - C(m + b - hi, b - 1 - hi)
        res += COMB[m + b - pre][b - 1 - pre] - COMB[m + b - hi][b - 1 - hi];
        pre = hi;
    }

    if check_s && is_non_dec(&digits) {
        res += 1;
    }
    res
}

impl Solution {
    pub fn count_numbers(l: String, r: String, b: i32) -> i32 {
        // 小于 r+1 的合法数字个数 - 小于 l 的合法数字个数
        // 但本题用 calc(r, true) 表示 <= r 的个数，calc(l, false) 表示 < l 的个数
        let ans = calc(&r, b, true) - calc(&l, b, false);
        (ans.rem_euclid(MOD)) as i32
    }
}
```
