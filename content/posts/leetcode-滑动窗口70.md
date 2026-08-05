---
title: "leetcode-滑动窗口70"
date: 2026-07-18T11:02:33+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 最长合法子字符串的长度

给你一个字符串 word 和一个字符串数组 forbidden 。

如果一个字符串不包含 forbidden 中的任何字符串，我们称这个字符串是 合法 的。

请你返回字符串 word 的一个 最长合法子字符串 的长度。

子字符串 指的是一个字符串中一段连续的字符，它可以为空。


```
use std::collections::{HashMap, HashSet};

/// 反向前缀树节点（后缀树），用于快速查找以当前位置结尾的禁用词
struct TrieNode {
    children: HashMap<char, TrieNode>,
    is_end: bool,
}

impl TrieNode {
    fn new() -> Self {
        Self {
            children: HashMap::new(),
            is_end: false,
        }
    }

    /// 反向插入禁用词，便于从右向左匹配
    fn insert_reversed(&mut self, word: &str) {
        let mut node = self;
        for ch in word.chars().rev() {
            node = node.children.entry(ch).or_insert_with(TrieNode::new);
        }
        node.is_end = true;
    }
}

impl Solution {
    pub fn longest_valid_substring(word: String, forbidden: Vec<String>) -> i32 {
        // 构建反向字典树
        let mut root = TrieNode::new();
        for s in &forbidden {
            root.insert_reversed(s);
        }

        let chars: Vec<char> = word.chars().collect();
        let n = chars.len();
        let mut max_len = 0;
        let mut left = 0; // 当前合法窗口的左边界

        // 滑动窗口，right 是窗口右边界（包含）
        for right in 0..n {
            // 从当前位置向左查找最长的禁用词后缀
            let mut node = &root;
            let mut min_left = left;

            // 限制查找长度，避免过长的无效匹配
            let max_check = right - left + 1;
            for offset in 0..max_check.min(10) {
                let idx = right - offset;
                if let Some(next) = node.children.get(&chars[idx]) {
                    node = next;
                    if node.is_end {
                        // 找到禁用词，窗口左边界必须移到禁用词之后
                        min_left = idx + 1;
                        break;
                    }
                } else {
                    break;
                }
            }

            left = left.max(min_left);
            max_len = max_len.max(right - left + 1);
        }

        max_len as i32
    }
}
```
