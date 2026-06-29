---
title: "leetcode-字典树36"
date: 2026-06-02T10:23:08+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 单词的压缩编码

```

```
单词数组 words 的 有效编码 由任意助记字符串 s 和下标数组 indices 组成，且满足：

words.length == indices.length
助记字符串 s 以 '#' 字符结尾
对于每个下标 indices[i] ，s 的一个从 indices[i] 开始、到下一个 '#' 字符结束（但不包括 '#'）的 子字符串 恰好与 words[i] 相等

给定一个单词数组 words ，返回成功对 words 进行编码的最小助记字符串 s 的长度 。


```
use std::collections::HashSet;

impl Solution {
    /// 计算最短引用编码的长度
    ///
    /// 编码规则：将单词列表编码成一个字符串，每个单词后跟一个'#'号
    /// 目标是找到最短的编码字符串，使得每个单词都是编码字符串中某个'#'结尾的子串
    ///
    /// # 算法原理
    /// 如果一个单词是另一个单词的后缀，那么它不需要单独编码
    /// 通过移除所有后缀单词，剩下的单词就是必须编码的
    ///
    /// # 参数
    /// * `words` - 要编码的单词列表
    ///
    /// # 返回
    /// 最短编码字符串的长度
    ///
    /// # 示例
    /// ```
    /// let words = vec!["time".to_string(), "me".to_string(), "bell".to_string()];
    /// let result = Solution::minimum_length_encoding(words);
    /// assert_eq!(result, 10); // 编码为 "time#bell#" 或 "bell#time#"
    /// ```
    pub fn minimum_length_encoding(words: Vec<String>) -> i32 {
        let mut unique_words: HashSet<&str> = words.iter().map(|s| s.as_str()).collect();

        // 移除所有是其他单词后缀的单词
        for word in &words {
            for k in 1..word.len() {
                unique_words.remove(&word[k..]);
            }
        }

        // 计算总长度：每个单词长度 + 1（'#'符号）
        unique_words.iter().map(|w| w.len() + 1).sum::<usize>() as i32
    }
}
```
