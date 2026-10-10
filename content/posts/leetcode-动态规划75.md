---
title: "leetcode-动态规划75"
date: 2026-10-05T20:39:59+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 分割回文串 III

给你一个由小写字母组成的字符串 s，和一个整数 k。

请你按下面的要求分割字符串：

首先，你可以将 s 中的部分字符修改为其他的小写英文字母。

接着，你需要把 s 分割成 k 个非空且不相交的子串，并且每个子串都是回文串。

请返回以这种方式分割字符串所需修改的最少字符数。


```
impl Solution {
    /// 1278. 分割回文串 III
    ///
    /// 第一步：预处理 cost[i][j] —— 把 s[i..=j] 变成回文所需的最少修改字符数。
    ///   转移：cost[i][j] = cost[i+1][j-1] + (s[i] != s[j])
    ///   按区间长度从小到大递推，长度 1 的 cost 为 0。
    ///
    /// 第二步：区间 DP。
    ///   dp[i][j]：把前 i 个字符分割成 j 个回文子串的最少修改数。
    ///   枚举最后一段起点 p（p 从 j-1 到 i-1）：
    ///     dp[i][j] = min(dp[p][j-1] + cost[p][i-1])
    ///   j == 1 时即整段作为回文串：dp[i][1] = cost[0][i-1]。
    ///   答案 dp[n][k]。
    ///
    /// 时间 O(n^2 + k*n^2)，空间 O(n^2)。用 u16 存代价，进一步省内存。
    pub fn palindrome_partition(s: String, k: i32) -> i32 {
        let n = s.len();
        let k = k as usize;
        let bytes = s.as_bytes();

        // cost[i][j]：s[i..=j] 变成回文的最少修改数
        let mut cost = vec![vec![0u16; n]; n];
        for span in 2..=n {
            for i in 0..=n - span {
                let j = i + span - 1;
                cost[i][j] = cost[i + 1][j - 1] + (bytes[i] != bytes[j]) as u16;
            }
        }

        // dp[i][j]：前 i 个字符分成 j 个回文子串的最少修改数
        let mut dp = vec![vec![u16::MAX; k + 1]; n + 1];
        dp[0][0] = 0;

        for i in 1..=n {
            let jmax = k.min(i); // 前 i 个字符最多分成 i 段
            for j in 1..=jmax {
                if j == 1 {
                    dp[i][1] = cost[0][i - 1];
                } else {
                    // 最后一段为 s[p..i]，前面 p 个字符分成 j-1 段
                    // p 至少 j-1（每段非空），至多 i-1
                    let mut best = u16::MAX;
                    for p in (j - 1)..i {
                        let prev = dp[p][j - 1];
                        if prev == u16::MAX {
                            continue;
                        }
                        let cand = prev + cost[p][i - 1];
                        if cand < best {
                            best = cand;
                        }
                    }
                    dp[i][j] = best;
                }
            }
        }

        dp[n][k] as i32
    }
}
```
