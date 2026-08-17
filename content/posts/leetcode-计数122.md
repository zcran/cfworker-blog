---
title: "leetcode-计数122"
date: 2026-08-01T10:40:57+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 前缀连接组的数目

给你一个字符串数组 words 和一个整数 k。

如果两个位于 不同下标 的单词 a 和 b 满足 a[0..k-1] == b[0..k-1]，则称它们是 前缀连接的。

一个 连接组 是指一组单词，其中每对单词都是前缀连接的。

返回从给定的单词中形成包含 至少 两个单词的 连接组数目 。

注意：

长度小于 k 的单词不能加入任何组，应被忽略。
重复的字符串被视为不同的单词。
字符串的 前缀 是指从字符串开头开始并延伸到其中任意位置的子字符串。


```
use std::collections::HashMap;

impl Solution {
    /// 统计包含至少两个单词的连接组数目。
    /// 连接组定义：组内每对单词的前 k 个字符相同。
    pub fn prefix_connected(words: Vec<String>, k: i32) -> i32 {
        let k = k as usize;
        let mut freq = HashMap::new();

        for word in &words {
            // 长度不足 k 的单词被忽略
            if let Some(prefix) = word.get(..k) {
                *freq.entry(prefix.to_string()).or_insert(0) += 1;
            }
        }

        // 出现次数大于 1 的前缀才能形成连接组
        freq.values().filter(|&&c| c > 1).count() as i32
    }
}
```
