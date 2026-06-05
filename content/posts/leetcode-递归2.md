---
title: "leetcode-递归2"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 正则表达式匹配


给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 '.' 和 '*' 的正则表达式匹配。

'.' 匹配任意单个字符
'*' 匹配零个或多个前面的那一个元素

返回一个布尔值，表示匹配是否覆盖整个输入字符串（而非部分）。


```
impl Solution {
    /// 正则表达式匹配（支持 '.' 和 '*'）
    ///
    /// # 状态定义
    /// dp[i][j] = s[0..i] 与 p[0..j] 是否匹配
    ///
    /// # 状态转移
    /// - 如果 p[j-1] != '*': dp[i][j] = match(i, j) && dp[i-1][j-1]
    /// - 如果 p[j-1] == '*':
    ///   - 匹配 0 次: dp[i][j-2]
    ///   - 匹配 >=1 次: match(i, j-1) && dp[i-1][j]
    pub fn is_match(s: String, p: String) -> bool {
        let s = s.as_bytes();
        let p = p.as_bytes();
        let (m, n) = (s.len(), p.len());

        // 使用 Vec<Vec<bool>> 但优化内存：只保留两行（滚动数组）
        let mut dp = vec![vec![false; n + 1]; 2];
        dp[0][0] = true;

        // 初始化首行：处理 p 中类似 "a*" 可匹配空串的情况
        for j in 1..=n {
            dp[0][j] = p[j - 1] == b'*' && dp[0][j - 2];
        }

        for i in 1..=m {
            let cur = i & 1;      // 当前行索引
            let prev = cur ^ 1;   // 上一行索引
            dp[cur][0] = false;   // 空模式无法匹配非空串

            for j in 1..=n {
                dp[cur][j] = if p[j - 1] == b'*' {
                    // 匹配 0 次 或 匹配多次
                    dp[cur][j - 2] || (Self::char_match(s[i - 1], p[j - 2]) && dp[prev][j])
                } else {
                    Self::char_match(s[i - 1], p[j - 1]) && dp[prev][j - 1]
                };
            }
        }

        dp[m & 1][n]
    }

    #[inline(always)]
    fn char_match(sc: u8, pc: u8) -> bool {
        pc == b'.' || sc == pc
    }
}
```
