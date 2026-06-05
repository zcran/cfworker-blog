---
title: "leetcode-组合数学33"
date: 2026-05-24T09:54:14+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 统计平衡排列的数目


给你一个字符串 num 。如果一个数字字符串的奇数位下标的数字之和与偶数位下标的数字之和相等，那么我们称这个数字字符串是 平衡的 。

请你返回 num 不同排列 中，平衡 字符串的数目。

由于答案可能很大，请你将答案对 10^9 + 7 取余 后返回。

一个字符串的 排列 指的是将字符串中的字符打乱顺序后连接得到的字符串。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn count_balanced_permutations(num: String) -> i32 {
        // 统计每个数字的出现次数
        let mut cnt = [0; 10];
        let mut total_sum = 0;
        for ch in num.chars() {
            let d = ch.to_digit(10).unwrap() as usize;
            cnt[d] += 1;
            total_sum += d;
        }

        // 总和为奇数时不可能平衡
        if total_sum % 2 != 0 {
            return 0;
        }
        let target = total_sum / 2;          // 奇数位需要达到的和
        let n = num.len();                   // 字符串长度
        let odd_pos = (n + 1) / 2;           // 奇数位置个数（位置从1开始编号）
        let even_pos = n / 2;                // 偶数位置个数

        // 预计算组合数 C[i][j] (i, j <= odd_pos)
        let mut comb = vec![vec![0; odd_pos + 1]; odd_pos + 1];
        for i in 0..=odd_pos {
            comb[i][0] = 1;
            comb[i][i] = 1;
            for j in 1..i {
                comb[i][j] = (comb[i-1][j] + comb[i-1][j-1]) % MOD;
            }
        }

        // 后缀和：psum[d] = cnt[d] + cnt[d+1] + ... + cnt[9]
        let mut psum = [0; 11];
        for d in (0..=9).rev() {
            psum[d] = psum[d+1] + cnt[d];
        }

        // dp[curr][odd_remain] = 方案数
        let max_odd = odd_pos;
        let mut dp = vec![vec![0; max_odd + 1]; target + 1];
        dp[0][max_odd] = 1;   // 初始状态：和=0，剩余奇数位数为总奇数位数

        // 依次处理每个数字 d
        for d in 0..=9 {
            if cnt[d] == 0 {
                continue;
            }
            let mut new_dp = vec![vec![0; max_odd + 1]; target + 1];
            for curr in 0..=target {
                for odd_rem in 0..=max_odd {
                    let ways = dp[curr][odd_rem];
                    if ways == 0 {
                        continue;
                    }
                    // 剩余未处理的数字总数 = psum[d]
                    let total_rem = psum[d] as i32;
                    let odd_rem_i32 = odd_rem as i32;
                    let even_rem = total_rem - odd_rem_i32;   // 剩余偶数位需放的数字个数
                    // 当前数字最多能放几个到奇数位？
                    let max_i = (cnt[d] as i32).min(odd_rem_i32);
                    let min_i = (cnt[d] as i32 - even_rem).max(0);
                    if min_i > max_i {
                        continue;
                    }
                    for i in min_i..=max_i {
                        let i_usize = i as usize;
                        let new_curr = curr + i_usize * d;
                        if new_curr > target {
                            continue;
                        }
                        let new_odd_rem = odd_rem - i_usize;
                        // 组合数：从剩余奇数位中选 i 个给当前数字，从剩余偶数位中选 cnt[d]-i 个
                        let ways_comb = comb[odd_rem][i_usize] * comb[even_rem as usize][cnt[d] - i_usize] % MOD;
                        new_dp[new_curr][new_odd_rem] = (new_dp[new_curr][new_odd_rem] + ways * ways_comb) % MOD;
                    }
                }
            }
            dp = new_dp;
        }

        // 最终状态：和 = target，剩余奇数位 = 0
        dp[target][0] as i32
    }
}
```
