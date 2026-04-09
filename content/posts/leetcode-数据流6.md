---
title: "leetcode-数据流6"
date: 2026-04-06T20:57:54+08:00
tags: ["leetcode", "数据流"]
draft: false
---


## 字符流

设计一个算法：接收一个字符流，并检查这些字符的后缀是否是字符串数组 words 中的一个字符串。

例如，words = ["abc", "xyz"] 且字符流中逐个依次加入 4 个字符 'a'、'x'、'y' 和 'z' ，你所设计的算法应当可以检测到 "axyz" 的后缀 "xyz" 与 words 中的字符串 "xyz" 匹配。

按下述要求实现 StreamChecker 类：

StreamChecker(String[] words) ：构造函数，用字符串数组 words 初始化数据结构。
boolean query(char letter)：从字符流中接收一个新字符，如果字符流中的任一非空后缀能匹配 words 中的某一字符串，返回 true ；否则，返回 false。


```
use std::collections::{HashMap, VecDeque};

/// 反向 Trie 节点（后缀匹配树）
struct Node {
    is_end: bool,
    children: HashMap<char, Box<Node>>,
}

impl Node {
    fn new() -> Self {
        Self {
            is_end: false,
            children: HashMap::new(),
        }
    }

    /// 插入一个单词（自动反转后存储）
    fn insert(&mut self, word: &str) {
        let mut node = self;
        for c in word.chars().rev() {
            node = node.children.entry(c).or_insert_with(|| Box::new(Node::new()));
        }
        node.is_end = true;
    }
}

/// 字符流查询器
pub struct StreamChecker {
    max_len: usize,
    root: Node,
    /// 使用 VecDeque 实现 O(1) 头部移除
    recent: VecDeque<char>,
}

impl StreamChecker {
    /// 用单词列表初始化
    pub fn new(words: Vec<String>) -> Self {
        let mut root = Node::new();
        let mut max_len = 0;

        for word in &words {
            max_len = max_len.max(word.len());
            root.insert(word);
        }

        Self {
            max_len,
            root,
            recent: VecDeque::with_capacity(max_len),
        }
    }

    /// 输入一个新字母，判断当前流末尾是否匹配任意单词
    pub fn query(&mut self, letter: char) -> bool {
        // 维护滑动窗口
        if self.recent.len() == self.max_len {
            self.recent.pop_front();
        }
        self.recent.push_back(letter);

        // 纯函数后缀匹配（核心逻辑）
        Self::suffix_match(&self.root, &self.recent)
    }

    /// 函数式后缀匹配：从最新字符开始匹配反向 Trie
    fn suffix_match(root: &Node, recent: &VecDeque<char>) -> bool {
        let mut node = root;

        for &ch in recent.iter().rev() {
            match node.children.get(&ch) {
                Some(next) => node = next,
                None => return false,
            }
            if node.is_end {
                return true;
            }
        }
        false
    }
}

```



题目需求：每次给出一个字母，查询最近输入的字符序列（从某个位置到末尾） 是否以某个单词结尾。
解决技巧：

将所有单词反转后插入 Trie（insert 里用了 chars().rev()）。
每次 query 时，将新字母加到 last_chars 末尾，然后从该序列末尾向前（即按时间从新到旧）在 Trie 中匹配。
这样匹配到的路径，对应原单词的正向顺序。
