---
title: "leetcode-回溯18"
date: 2026-07-04T10:22:02+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 分割回文串

给你一个字符串 s，请你将 s 分割成一些 子串，使每个子串都是 回文串 。返回 s 所有可能的分割方案。


```
impl Solution {
    pub fn partition(s: String) -> Vec<Vec<String>> {
        let s = s.as_bytes();
        let n = s.len();

        // 预处理回文串：dp[i][j] 表示 s[i..=j] 是否为回文串
        let mut dp = vec![vec![true; n]; n];
        for i in (0..n).rev() {
            for j in i + 1..n {
                dp[i][j] = s[i] == s[j] && dp[i + 1][j - 1];
            }
        }

        let mut result = Vec::new();
        let mut path = Vec::new();
        Self::dfs(0, &s, &dp, &mut path, &mut result);
        result
    }

    /// 深度优先搜索所有分割方案
    /// - start: 当前切割的起始位置
    /// - s: 原始字符串的字节数组
    /// - dp: 回文串预处理表
    /// - path: 当前已分割的回文子串
    /// - result: 所有分割方案
    fn dfs(
        start: usize,
        s: &[u8],
        dp: &[Vec<bool>],
        path: &mut Vec<String>,
        result: &mut Vec<Vec<String>>,
    ) {
        // 到达末尾，找到一个完整的分割方案
        if start == s.len() {
            result.push(path.clone());
            return;
        }

        // 尝试从 start 开始的所有可能回文子串
        for end in start..s.len() {
            if dp[start][end] {
                // 当前子串是回文串，加入路径
                let substr = String::from_utf8(s[start..=end].to_vec()).unwrap();
                path.push(substr);

                // 递归处理剩余部分
                Self::dfs(end + 1, s, dp, path, result);

                // 回溯
                path.pop();
            }
        }
    }
}
```
