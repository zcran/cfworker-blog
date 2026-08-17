---
title: "leetcode-模拟135"
date: 2026-08-08T11:31:17+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 带权单词映射

给你一个字符串数组 words，其中每个字符串表示一个由小写英文字母组成的单词。

同时给你一个长度为 26 的整数数组 weights，其中 weights[i] 表示第 i 个小写英文字母的权重。

单词的 权重 定义为其所有字符权重的 总和。

对于每个单词，将其权重对 26 取模，并将结果按字母倒序映射到一个小写英文字母（0 -> 'z', 1 -> 'y', ..., 25 -> 'a'）。

返回一个由所有单词映射后的字符按顺序连接而成的字符串。


```
impl Solution {
    /// 将每个单词的权重和映射为倒序字母
    ///
    /// 优化点：
    /// - 累加时即时取模，防止 i32 溢出（超长单词场景）
    /// - as_bytes() 避免 UTF-8 解码开销
    /// - 预分配精确容量，零额外内存分配
    pub fn map_word_weights(words: Vec<String>, weights: Vec<i32>) -> String {
        let mut ans = String::with_capacity(words.len());

        for word in &words {
            let mut sum = 0i32;
            for &b in word.as_bytes() {
                // 即时取模：sum 始终保持在 [0, 25] 范围内，彻底避免溢出
                sum = (sum + weights[(b - b'a') as usize]) % 26;
            }
            // 倒序映射：0->'z', 1->'y', ..., 25->'a'
            ans.push((b'z' - sum as u8) as char);
        }

        ans
    }
}
```
