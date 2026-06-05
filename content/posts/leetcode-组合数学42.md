---
title: "leetcode-组合数学42"
date: 2026-05-24T09:54:14+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 最小回文排列 II

给你一个 回文 字符串 s 和一个整数 k。

返回 s 的按字典序排列的 第 k 小 回文排列。如果不存在 k 个不同的回文排列，则返回空字符串。

注意： 产生相同回文字符串的不同重排视为相同，仅计为一次。

如果一个字符串从前往后和从后往前读都相同，那么这个字符串是一个 回文 字符串。

排列 是字符串中所有字符的重排。

如果字符串 a 按字典序小于字符串 b，则表示在第一个不同的位置，a 中的字符比 b 中的对应字符在字母表中更靠前。
如果在前 min(a.length, b.length) 个字符中没有区别，则较短的字符串按字典序更小。

```
impl Solution {
    /// 返回字符串 s 的第 k 小回文排列（不同排列只计一次）。
    /// 若不存在 k 个不同的回文排列，则返回空字符串。
    pub fn smallest_palindrome(s: String, k: i32) -> String {
        let s = s.as_bytes();
        let n = s.len();
        let m = n / 2;                               // 左半部分长度

        // 统计左半部分各字母出现的次数（因为 s 是回文，所以左右对称）
        let mut cnt = [0; 26];
        for &ch in &s[..m] {
            cnt[(ch - b'a') as usize] += 1;
        }

        let mid_char = if n % 2 == 1 { Some(s[n / 2]) } else { None };
        let mut k = k as i64;

        // 组合数 C(n, m)，若计算结果超过 limit 则返回 limit（避免溢出）
        let comb = |n: usize, m: usize, limit: i64| -> i64 {
            if limit == 0 {
                return 0;
            }
            if m > n {
                return 0;
            }
            if m == 0 || m == n {
                return 1.min(limit);
            }
            let m = m.min(n - m);
            let mut res = 1u128;
            let limit = limit as u128;
            for i in 1..=m {
                let numerator = (n + 1 - i) as u128;
                // res = res * numerator / i
                if let Some(mul) = res.checked_mul(numerator) {
                    res = mul / (i as u128);
                } else {
                    // 乘法溢出，结果肯定超过 limit
                    return limit as i64;
                }
                if res > limit {
                    return limit as i64;
                }
            }
            res as i64
        };

        // 当前频率下，长度为 sz 的字符串的不同排列总数（不超过 limit）
        let perm = |cnt: &[i32; 26], sz: usize, limit: i64| -> i64 {
            if limit == 0 {
                return 0;
            }
            let mut res = 1u128;
            let mut remain = sz;
            let limit = limit as u128;
            for &c in cnt {
                if c == 0 {
                    continue;
                }
                let c = c as usize;
                if c > remain {
                    return 0;
                }
                let ways = comb(remain, c, limit as i64) as u128;
                if ways == 0 {
                    return 0;
                }
                if let Some(mul) = res.checked_mul(ways) {
                    res = mul;
                } else {
                    return limit as i64;
                }
                if res > limit {
                    return limit as i64;
                }
                remain -= c;
            }
            if remain != 0 {
                0
            } else {
                res.min(limit) as i64
            }
        };

        // 总排列数不足 k 时直接返回空串
        if perm(&cnt, m, k) < k {
            return String::new();
        }

        // 逐位构造左半部分（字典序第 k 小）
        let mut left = Vec::with_capacity(m);
        let mut cnt = cnt;
        for i in 0..m {
            let remain = m - i - 1;
            for j in 0..26 {
                if cnt[j] == 0 {
                    continue;
                }
                cnt[j] -= 1;
                let p = perm(&cnt, remain, k);
                if p >= k {
                    left.push(b'a' + j as u8);
                    break;
                } else {
                    k -= p;
                    cnt[j] += 1;
                }
            }
        }

        // 拼接完整的回文字符串
        let mut ans = Vec::with_capacity(n);
        ans.extend_from_slice(&left);
        if let Some(mid) = mid_char {
            ans.push(mid);
        }
        ans.extend(left.iter().rev());
        String::from_utf8(ans).unwrap()
    }
}
```
