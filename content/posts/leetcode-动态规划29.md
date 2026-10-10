---
title: "leetcode-动态规划29"
date: 2026-10-05T20:39:55+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 学生出勤记录 II

可以用字符串表示一个学生的出勤记录，其中的每个字符用来标记当天的出勤情况（缺勤、迟到、到场）。记录中只含下面三种字符：
'A'：Absent，缺勤
'L'：Late，迟到
'P'：Present，到场

如果学生能够 同时 满足下面两个条件，则可以获得出勤奖励：

· 按 总出勤 计，学生缺勤（'A'）严格 少于两天。
· 学生 不会 存在 连续 3 天或 连续 3 天以上的迟到（'L'）记录。

给你一个整数 n ，表示出勤记录的长度（次数）。请你返回记录长度为 n 时，可能获得出勤奖励的记录情况 数量 。答案可能很大，所以返回对 109 + 7 取余 的结果。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn check_record(n: i32) -> i32 {
        let n = n as usize;

        // dp[j][k]：当前长度下，状态为 (已用缺勤数 j, 末尾连续迟到数 k) 的方案数
        //   j ∈ {0, 1}  表示已使用的 A 数量（A 数必须 < 2）
        //   k ∈ {0, 1, 2} 表示末尾连续 L 的数量（必须 < 3）
        let mut dp = [[0i64; 3]; 2];
        dp[0][0] = 1;

        for _ in 0..n {
            let mut next = [[0i64; 3]; 2];
            for j in 0..2 {
                for k in 0..3 {
                    let cur = dp[j][k];
                    if cur == 0 {
                        continue;
                    }
                    // 今天到场 P：连续 L 清零
                    next[j][0] = (next[j][0] + cur) % MOD;
                    // 今天缺勤 A：缺勤数加 1（仅当 j == 0 时才合法）
                    if j == 0 {
                        next[1][0] = (next[1][0] + cur) % MOD;
                    }
                    // 今天迟到 L：连续 L 加 1（仅当 k < 2 时才合法）
                    if k < 2 {
                        next[j][k + 1] = (next[j][k + 1] + cur) % MOD;
                    }
                }
            }
            dp = next;
        }

        // 汇总所有合法状态
        let mut ans = 0i64;
        for j in 0..2 {
            for k in 0..3 {
                ans = (ans + dp[j][k]) % MOD;
            }
        }

        ans as i32
    }
}
```
