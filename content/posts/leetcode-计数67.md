---
title: "leetcode-计数67"
date: 2026-08-01T10:40:54+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 最多单词数的发件人

给你一个聊天记录，共包含 n 条信息。给你两个字符串数组 messages 和 senders ，其中 messages[i] 是 senders[i] 发出的一条 信息 。

一条 信息 是若干用单个空格连接的 单词 ，信息开头和结尾不会有多余空格。发件人的 单词计数 是这个发件人总共发出的 单词数 。注意，一个发件人可能会发出多于一条信息。

请你返回发出单词数 最多 的发件人名字。如果有多个发件人发出最多单词数，请你返回 字典序 最大的名字。

注意：

字典序里，大写字母小于小写字母。
"Alice" 和 "alice" 是不同的名字。


```
use std::collections::HashMap;

impl Solution {
    pub fn largest_word_count(messages: Vec<String>, senders: Vec<String>) -> String {
        // 统计每个发件人的总单词数
        let mut word_count = HashMap::with_capacity(senders.len());

        for (msg, sender) in messages.into_iter().zip(senders.into_iter()) {
            // 计算本条消息的单词数（按空格分割）
            let words = msg.split_whitespace().count();
            *word_count.entry(sender).or_insert(0) += words;
        }

        // 找出单词数最多的人，如果相同则取字典序最大的
        word_count
            .into_iter()
            .max_by(|a, b| a.1.cmp(&b.1).then_with(|| a.0.cmp(&b.0)))
            .unwrap()
            .0
    }
}
```
