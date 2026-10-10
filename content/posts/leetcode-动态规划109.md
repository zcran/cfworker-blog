---
title: "leetcode-动态规划109"
date: 2026-10-05T20:40:01+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 统计回文子序列数目

给你数字字符串 s ，请你返回 s 中长度为 5 的 回文子序列 数目。由于答案可能很大，请你将答案对 10^9 + 7 取余 后返回。

提示：

如果一个字符串从前往后和从后往前读相同，那么它是 回文字符串 。
子序列是一个字符串中删除若干个字符后，不改变字符顺序，剩余字符构成的字符串。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn count_palindromes(s: String) -> i32 {
        let s = s.as_bytes();

        // 由于字符仅 '0'-'9'，用固定大小数组代替哈希表，提升速度与内存
        // dp1[a]          : 子序列 "a" 的数量
        // dp2[a][b]       : 子序列 "ab" 的数量
        // dp3[a][b][c]    : 子序列 "abc" 的数量
        // dp4[a][b][c][b] : 子序列 "abcb" 的数量
        let mut dp1 = [0i64; 10];
        let mut dp2 = [[0i64; 10]; 10];
        let mut dp3 = [[[0i64; 10]; 10]; 10];
        let mut dp4 = [[[[0i64; 10]; 10]; 10]; 10];

        let mut ans = 0i64;

        for &ch in s {
            let x = (ch - b'0') as usize;

            // 当前字符作为回文最后一个字符 a，累加所有 "abcb" 的数量
            for b in 0..10 {
                for c in 0..10 {
                    ans = (ans + dp4[x][b][c][b]) % MOD;
                }
            }

            // 更新 dp4：当前字符作为第四个字符 b，接在 "abc" 之后
            for a in 0..10 {
                for c in 0..10 {
                    dp4[a][x][c][x] = (dp4[a][x][c][x] + dp3[a][x][c]) % MOD;
                }
            }

            // 更新 dp3：当前字符作为第三个字符 c，接在 "ab" 之后
            for a in 0..10 {
                for b in 0..10 {
                    dp3[a][b][x] = (dp3[a][b][x] + dp2[a][b]) % MOD;
                }
            }

            // 更新 dp2：当前字符作为第二个字符 b，接在 "a" 之后
            for a in 0..10 {
                dp2[a][x] = (dp2[a][x] + dp1[a]) % MOD;
            }

            // 更新 dp1：当前字符作为单个字符 a
            dp1[x] += 1;
        }

        ans as i32
    }
}
```
