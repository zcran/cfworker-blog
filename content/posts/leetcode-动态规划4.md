---
title: "leetcode-动态规划4"
date: 2026-10-05T20:39:54+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 交错字符串

给定三个字符串 s1、s2、s3，请你帮忙验证 s3 是否是由 s1 和 s2 交错 组成的。

两个字符串 s 和 t 交错 的定义与过程如下，其中每个字符串都会被分割成若干 非空 子字符串：

s = s1 + s2 + ... + sn
t = t1 + t2 + ... + tm
|n - m| <= 1
交错 是 s1 + t1 + s2 + t2 + s3 + t3 + ... 或者 t1 + s1 + t2 + s2 + t3 + s3 + ...

注意：a + b 意味着字符串 a 和 b 连接。




```
impl Solution {
    pub fn is_interleave(s1: String, s2: String, s3: String) -> bool {
        let (a, b, c) = (s1.as_bytes(), s2.as_bytes(), s3.as_bytes());
        let (n, m) = (a.len(), b.len());

        // 长度不匹配直接返回 false
        if n + m != c.len() {
            return false;
        }

        // 用较短的一侧作为 DP 维度，减小空间
        let (a, b, n, m) = if n >= m {
            (a, b, n, m)
        } else {
            (b, a, m, n)
        };

        // dp[j]：s1 的前 i 个字符与 s2 的前 j 个字符能否交错组成 s3 的前 i+j 个字符
        let mut dp = vec![false; m + 1];
        dp[0] = true;

        // 初始化第一行（只使用 s2）
        for j in 1..=m {
            dp[j] = dp[j - 1] && b[j - 1] == c[j - 1];
        }

        for i in 1..=n {
            // 第一列（只使用 s1）
            dp[0] = dp[0] && a[i - 1] == c[i - 1];
            for j in 1..=m {
                let k = i + j - 1;
                dp[j] = (dp[j] && a[i - 1] == c[k])
                    || (dp[j - 1] && b[j - 1] == c[k]);
            }
        }

        dp[m]
    }
}
```
