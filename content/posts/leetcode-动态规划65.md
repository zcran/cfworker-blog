---
title: "leetcode-动态规划65"
date: 2026-10-05T20:39:58+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 最短公共超序列

给你两个字符串 str1 和 str2，返回同时以 str1 和 str2 作为 子序列 的最短字符串。如果答案不止一个，则可以返回满足条件的 任意一个 答案。

如果从字符串 t 中删除一些字符（也可能不删除），可以得到字符串 s ，那么 s 就是 t 的一个子序列。




```
impl Solution {
    /// 1092. 最短公共超序列
    ///
    /// 先求 str1 与 str2 的最长公共子序列（LCS）长度表 dp。
    /// 然后从右下角回溯构造答案：
    ///   - 两字符相等：该字符属于 LCS，放入答案，同时左移两指针
    ///   - 不等：沿 dp 值较大的一侧移动指针，把对应字符放入答案
    ///   - 某一串先耗尽：把另一串剩余字符依次放入
    ///
    /// 答案要求顺序正确，因此从后往前收集字符，最后整体反转，
    /// 避免原版每次 insert(0, ..) 的 O(n) 搬移。
    pub fn shortest_common_supersequence(str1: String, str2: String) -> String {
        let (s1, s2) = (str1.as_bytes(), str2.as_bytes());
        let (m, n) = (s1.len(), s2.len());

        // dp[i][j]：s1[..i] 与 s2[..j] 的 LCS 长度
        let mut dp = vec![vec![0u16; n + 1]; m + 1];
        for i in 0..m {
            for j in 0..n {
                dp[i + 1][j + 1] = if s1[i] == s2[j] {
                    dp[i][j] + 1
                } else {
                    dp[i + 1][j].max(dp[i][j + 1])
                };
            }
        }

        // 从 (m, n) 回溯，倒序收集字符
        let mut res = Vec::with_capacity(m + n);
        let (mut i, mut j) = (m, n);
        while i > 0 && j > 0 {
            if s1[i - 1] == s2[j - 1] {
                res.push(s1[i - 1]);
                i -= 1;
                j -= 1;
            } else if dp[i - 1][j] >= dp[i][j - 1] {
                res.push(s1[i - 1]);
                i -= 1;
            } else {
                res.push(s2[j - 1]);
                j -= 1;
            }
        }
        // 处理剩余字符
        while i > 0 {
            res.push(s1[i - 1]);
            i -= 1;
        }
        while j > 0 {
            res.push(s2[j - 1]);
            j -= 1;
        }

        res.reverse();
        String::from_utf8(res).unwrap()
    }
}
```
