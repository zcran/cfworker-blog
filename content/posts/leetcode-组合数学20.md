---
title: "leetcode-组合数学20"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 统计一个字符串的 k 子序列美丽值最大的数目

给你一个字符串 s 和一个整数 k 。

k 子序列指的是 s 的一个长度为 k 的 子序列 ，且所有字符都是 唯一 的，也就是说每个字符在子序列里只出现过一次。

定义 f(c) 为字符 c 在 s 中出现的次数。

k 子序列的 美丽值 定义为这个子序列中每一个字符 c 的 f(c) 之 和 。

比方说，s = "abbbdd" 和 k = 2 ，我们有：

f('a') = 1, f('b') = 3, f('d') = 2
s 的部分 k 子序列为：
"abbbdd" -> "ab" ，美丽值为 f('a') + f('b') = 4
"abbbdd" -> "ad" ，美丽值为 f('a') + f('d') = 3
"abbbdd" -> "bd" ，美丽值为 f('b') + f('d') = 5
请你返回一个整数，表示所有 k 子序列 里面 美丽值 是 最大值 的子序列数目。由于答案可能很大，将结果对 10^9 + 7 取余后返回。

一个字符串的子序列指的是从原字符串里面删除一些字符（也可能一个字符也不删除），不改变剩下字符顺序连接得到的新字符串。

注意：

f(c) 指的是字符 c 在字符串 s 的出现次数，不是在 k 子序列里的出现次数。
两个 k 子序列如果有任何一个字符在原字符串中的下标不同，则它们是两个不同的子序列。所以两个不同的 k 子序列可能产生相同的字符串。


```
impl Solution {
    const MOD: i64 = 1_000_000_007;

    /// 快速幂：计算 x^n mod MOD
    fn pow_mod(mut x: i64, mut n: i64) -> i64 {
        let mut res = 1;
        while n > 0 {
            if n & 1 == 1 {
                res = (res * x) % Self::MOD;
            }
            x = (x * x) % Self::MOD;
            n >>= 1;
        }
        res
    }

    /// 计算组合数 C(n, k)，其中 k ≤ 26，n 可能很大但用乘法直接算
    fn comb(mut n: i64, k: i32) -> i64 {
        let k = k as usize;
        let mut res = 1;
        // 递推公式：C(n, k) = n * (n-1) * ... * (n-k+1) / k!
        for i in 1..=k {
            // 分子乘 (n - i + 1)
            res = (res * ((n - i as i64 + 1) % Self::MOD)) % Self::MOD;
            // 除以 i 用乘法逆元（因为 MOD 是质数，i 与 MOD 互质）
            res = (res * Self::pow_mod(i as i64, Self::MOD - 2)) % Self::MOD;
        }
        res
    }

    pub fn count_k_subsequences_with_max_beauty(s: String, k: i32) -> i32 {
        let mut cnt = [0; 26];
        for &b in s.as_bytes() {
            cnt[(b - b'a') as usize] += 1;
        }

        // 收集所有非零频数，并按降序排列
        let mut freqs: Vec<i32> = cnt.into_iter().filter(|&c| c > 0).collect();
        freqs.sort_unstable_by(|a, b| b.cmp(a)); // 降序

        let mut k_remaining = k as usize;
        let mut ans = 1i64;

        // 遍历降序分组（相同频数连续出现）
        let mut i = 0;
        while i < freqs.len() {
            let f = freqs[i] as i64;
            // 统计当前频数的字符个数
            let mut same_cnt = 0;
            while i < freqs.len() && freqs[i] == f as i32 {
                same_cnt += 1;
                i += 1;
            }

            if same_cnt >= k_remaining {
                // 从本组中选择 k_remaining 个字符
                ans = ans * Self::pow_mod(f, k_remaining as i64) % Self::MOD;
                ans = ans * Self::comb(same_cnt as i64, k_remaining as i32) % Self::MOD;
                return ans as i32;
            } else {
                // 全部取用本组所有字符
                ans = ans * Self::pow_mod(f, same_cnt as i64) % Self::MOD;
                k_remaining -= same_cnt;
            }
        }

        // 无法选满 k 个不同字符
        0
    }
}
```
