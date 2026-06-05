---
title: "leetcode-组合数学30"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 统计好整数的数目


给你两个 正 整数 n 和 k 。

如果一个整数 x 满足以下条件，那么它被称为 k 回文 整数 。

x 是一个 回文整数 。
x 能被 k 整除。
如果一个整数的数位重新排列后能得到一个 k 回文整数 ，那么我们称这个整数为 好 整数。比方说，k = 2 ，那么 2020 可以重新排列得到 2002 ，2002 是一个 k 回文串，所以 2020 是一个好整数。而 1010 无法重新排列数位得到一个 k 回文整数。

请你返回 n 个数位的整数中，有多少个 好 整数。

注意 ，任何整数在重新排列数位之前或者之后 都不能 有前导 0 。比方说 1010 不能重排列得到 101 。


```
use std::collections::HashSet;

impl Solution {
    /// 返回所有 n 位好整数的个数。
    /// 好整数：重新排列数字后可以得到一个 k 回文整数（回文且被 k 整除）。
    pub fn count_good_integers(n: i32, k: i32) -> i64 {
        let n = n as usize;
        let k = k as i64;
        // 回文数前半部分的长度（n 位回文由前半部分镜像得到）
        let half_len = (n + 1) / 2;
        let start = 10_i64.pow((half_len - 1) as u32); // 最小的 half_len 位数
        let end = start * 10;
        let skip = n % 2; // 奇数时中间位不参与镜像

        // 用数字频率数组作为去重键，避免字符串排序
        let mut unique_counts = HashSet::new();

        // 枚举所有 n 位回文数
        for half in start..end {
            let half_str = half.to_string();
            // 后半部分 = 前半部分逆序，若 n 为奇数则跳过中间位
            let rev: String = half_str.chars().rev().skip(skip).collect();
            let pal_str = format!("{}{}", half_str, rev);
            let pal: i64 = pal_str.parse().unwrap();

            if pal % k == 0 {
                // 统计 0~9 每个数字的出现次数
                let mut cnt = [0usize; 10];
                for ch in pal_str.chars() {
                    cnt[ch.to_digit(10).unwrap() as usize] += 1;
                }
                unique_counts.insert(cnt);
            }
        }

        // 预计算阶乘
        let mut fact = vec![1i64; n + 1];
        for i in 1..=n {
            fact[i] = fact[i - 1] * i as i64;
        }

        let mut ans = 0i64;
        for cnt in unique_counts {
            // 计算该数字多重集能组成的无前导零的整数个数：
            // 总排列数减去首位为 0 的排列数，等价于 (n - cnt[0]) * (n-1)! / ∏ cnt[i]!
            let mut ways = (n as i64 - cnt[0] as i64) * fact[n - 1];
            for &c in &cnt {
                ways /= fact[c];
            }
            ans += ways;
        }
        ans
    }
}
```
