---
title: "leetcode-递归40"
date: 2026-05-26T10:00:18+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 模糊搜索验证


请设计一个程序来支持用户在文本编辑器中的模糊搜索功能。用户输入内容中可能使用到如下两种通配符：

'.' 匹配任意单个字符。
'*' 匹配零个或多个前面的那一个元素。


请返回用户输入内容 input 所有字符是否可以匹配原文字符串 article。

```
impl Solution {
    pub fn article_match(s: String, p: String) -> bool {
        let s_chars: Vec<char> = s.chars().collect();
        let p_chars: Vec<char> = p.chars().collect();
        let m = s_chars.len();
        let n = p_chars.len();

        // dp[i][j] 表示 s 的前 i 个字符和 p 的前 j 个字符是否匹配
        let mut dp = vec![vec![false; n + 1]; m + 1];

        // 空字符串和空模式串匹配
        dp[0][0] = true;

        // 处理空字符串匹配模式串的情况，如 "a*", "a*b*" 等
        for j in (2..=n).step_by(2) {
            if p_chars[j - 1] == '*' {
                dp[0][j] = dp[0][j - 2];
            } else {
                break;
            }
        }

        // 动态规划填表
        for i in 1..=m {
            for j in 1..=n {
                if p_chars[j - 1] == '*' {
                    // 当前模式字符是 '*'
                    // 检查 '*' 前面的字符
                    let prev_char = p_chars[j - 2];
                    if prev_char == '.' || prev_char == s_chars[i - 1] {
                        // 匹配0次或多次
                        dp[i][j] = dp[i][j - 2] || dp[i - 1][j];
                    } else {
                        // 只能匹配0次
                        dp[i][j] = dp[i][j - 2];
                    }
                } else {
                    // 当前模式字符不是 '*'
                    if p_chars[j - 1] == '.' || p_chars[j - 1] == s_chars[i - 1] {
                        dp[i][j] = dp[i - 1][j - 1];
                    } else {
                        dp[i][j] = false;
                    }
                }
            }
        }

        dp[m][n]
    }
}
```
