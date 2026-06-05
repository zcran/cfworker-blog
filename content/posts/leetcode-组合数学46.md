---
title: "leetcode-组合数学46"
date: 2026-05-24T09:54:14+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 二进制中恰好K个1的第N小整数


给你两个正整数 n 和 k。

返回一个整数，表示其二进制表示中 恰好 包含 k 个 1 的第 n 小的正整数。题目保证答案 严格小于 2^50。


```
impl Solution {
    /// 返回第 n 个有 k 个 1 的二进制数（n 从 1 开始计数）
    pub fn nth_smallest(mut n: i64, k: i32) -> i64 {
        const MX: usize = 50;
        // 预计算组合数 C[i][j] (i >= j)
        let mut comb = [[0i64; MX + 1]; MX];
        for i in 0..MX {
            comb[i][0] = 1;
            for j in 1..=i {
                comb[i][j] = comb[i - 1][j - 1] + comb[i - 1][j];
            }
        }

        let mut ans = 0i64;
        let mut k = k as usize;
        for i in (0..MX).rev() {
            if k == 0 {
                break;
            }
            let c = comb[i][k]; // 第 i 位填 0 时，剩余位的方案数
            if n > c {
                // 必须在这一位填 1
                n -= c;
                ans |= 1 << i;
                k -= 1;
            }
            // 否则填 0，无需操作
        }
        ans
    }
}
```
