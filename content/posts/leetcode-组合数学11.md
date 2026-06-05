---
title: "leetcode-组合数学11"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 使字符串有序的最少操作次数


给你一个字符串 s （下标从 0 开始）。你需要对 s 执行以下操作直到它变为一个有序字符串：

找到 最大下标 i ，使得 1 <= i < s.length 且 s[i] < s[i - 1] 。
找到 最大下标 j ，使得 i <= j < s.length 且对于所有在闭区间 [i, j] 之间的 k 都有 s[k] < s[i - 1] 。
交换下标为 i - 1和 j 处的两个字符。
将下标 i 开始的字符串后缀反转。

请你返回将字符串变成有序的最少操作次数。由于答案可能会很大，请返回它对 10^9 + 7 取余 的结果。


```
impl Solution {
    const MOD: i64 = 1_000_000_007;

    // 快速幂：计算 x^y mod MOD
    fn pow_mod(mut x: i64, mut y: i64) -> i64 {
        let mut res = 1;
        while y > 0 {
            if y & 1 == 1 {
                res = (res * x) % Self::MOD;
            }
            x = (x * x) % Self::MOD;
            y >>= 1;
        }
        res
    }

    pub fn make_string_sorted(s: String) -> i32 {
        let n = s.len();
        // 长度小于 2 的字符串不需要操作，排名为 0
        if n < 2 {
            return 0;
        }

        let bytes = s.as_bytes();
        // 阶乘数组和阶乘逆元数组
        let mut fac = vec![0i64; n + 1];
        let mut fac_inv = vec![0i64; n + 1];
        fac[0] = 1;
        for i in 1..=n {
            fac[i] = (fac[i - 1] * i as i64) % Self::MOD;
        }
        // 使用费马小定理计算 n! 的逆元，再递推出所有阶乘逆元
        fac_inv[n] = Self::pow_mod(fac[n], Self::MOD - 2);
        for i in (1..=n).rev() {
            fac_inv[i - 1] = (fac_inv[i] * i as i64) % Self::MOD;
        }

        // 统计每个字母的出现次数
        let mut freq = [0; 26];
        for &ch in bytes {
            freq[(ch - b'a') as usize] += 1;
        }

        let mut ans = 0i64;
        for (i, &ch) in bytes.iter().enumerate() {
            let idx = (ch - b'a') as usize;
            // 统计比当前字符小的字符总数（字典序排名基础）
            let rank = (0..idx).map(|j| freq[j]).sum::<usize>() as i64;
            // 当前剩余位置的总排列数： (n-i-1)! * rank / (∏ (freq[j]!))
            let mut cur = (rank * fac[n - i - 1]) % Self::MOD;
            for &cnt in &freq {
                cur = (cur * fac_inv[cnt as usize]) % Self::MOD;
            }
            ans = (ans + cur) % Self::MOD;
            // 当前字符计数减一，准备处理下一个位置
            freq[idx] -= 1;
        }
        ans as i32
    }
}
```
