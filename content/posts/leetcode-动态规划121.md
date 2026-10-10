---
title: "leetcode-动态规划121"
date: 2026-10-05T20:40:02+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 统计范围内的步进数字数目

给你两个正整数 low 和 high ，都用字符串表示，请你统计闭区间 [low, high] 内的 步进数字 数目。

如果一个整数相邻数位之间差的绝对值都 恰好 是 1 ，那么这个数字被称为 步进数字 。

请你返回一个整数，表示闭区间 [low, high] 之间步进数字的数目。

由于答案可能很大，请你将它对 10^9 + 7 取余 后返回。

注意：步进数字不能有前导 0 。


```
const MOD: i64 = 1_000_000_007;
const MAX_LEN: usize = 100;

/// H[len][d]: 长度为 len、第一位数字为 d 的步进数字个数
/// 递推：H[len][d] = H[len-1][d-1] + H[len-1][d+1]
/// 其中 H[0][d] = 1（空剩余部分），H[1][d] = 1（单个数字）
static H: [[i64; 10]; MAX_LEN + 1] = {
    let mut h = [[0i64; 10]; MAX_LEN + 1];

    let mut d = 0;
    while d < 10 {
        h[0][d] = 1;
        d += 1;
    }
    d = 0;
    while d < 10 {
        h[1][d] = 1;
        d += 1;
    }

    let mut len = 2;
    while len <= MAX_LEN {
        let mut d = 0;
        while d < 10 {
            let mut v = 0i64;
            if d > 0 {
                v += h[len - 1][d - 1];
            }
            if d < 9 {
                v += h[len - 1][d + 1];
            }
            h[len][d] = v % MOD;
            d += 1;
        }
        len += 1;
    }
    h
};

impl Solution {
    pub fn count_stepping_numbers(low: String, high: String) -> i32 {
        let lo = Self::count_upto(low.as_bytes());
        let hi = Self::count_upto(high.as_bytes());
        let low_valid = Self::is_stepping(low.as_bytes()) as i64;
        ((hi - lo + low_valid + MOD) % MOD) as i32
    }

    /// 统计 [0, s] 内步进数字的个数（s 为无前导零的十进制字符串）
    fn count_upto(s: &[u8]) -> i64 {
        let n = s.len();
        let mut cnt = 0i64;

        // 1. 所有长度严格小于 n 的步进数字
        let mut len = 1;
        while len < n {
            let mut d = 1; // 最高位不能为 0
            while d < 10 {
                cnt = (cnt + H[len][d]) % MOD;
                d += 1;
            }
            len += 1;
        }

        // 2. 长度等于 n，逐位固定前缀
        let mut prev: i32 = -1;
        let mut i = 0;
        while i < n {
            let cur = (s[i] - b'0') as i32;

            if i == 0 {
                // 最高位取 1..cur-1，剩余 n 位以该数字开头
                let mut d = 1;
                while d < cur {
                    cnt = (cnt + H[n][d as usize]) % MOD;
                    d += 1;
                }
                prev = cur;
            } else {
                // 当前位只能取 prev-1 或 prev+1，且需小于 cur
                if prev > 0 {
                    let d = prev - 1;
                    if d < cur {
                        cnt = (cnt + H[n - i][d as usize]) % MOD;
                    }
                }
                if prev < 9 {
                    let d = prev + 1;
                    if d < cur {
                        cnt = (cnt + H[n - i][d as usize]) % MOD;
                    }
                }
                // 若 s 本身在此位不再满足步进条件，终止
                if (cur - prev).abs() != 1 {
                    return cnt;
                }
                prev = cur;
            }
            i += 1;
        }

        // s 本身也是步进数字，补上
        (cnt + 1) % MOD
    }

    /// 判断字节序列是否为步进数字
    fn is_stepping(s: &[u8]) -> bool {
        let mut i = 1;
        while i < s.len() {
            if (s[i] as i32 - s[i - 1] as i32).abs() != 1 {
                return false;
            }
            i += 1;
        }
        true
    }
}
```
