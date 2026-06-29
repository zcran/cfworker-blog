---
title: "leetcode-字典树34"
date: 2026-06-02T10:23:08+08:00
tags: ["leetcode", "字典树"]
draft: false
---

## 实现 Trie (前缀树)


Trie（发音类似 "try"）或者说 前缀树 是一种树形数据结构，用于高效地存储和检索字符串数据集中的键。这一数据结构有相当多的应用情景，例如自动补完和拼写检查。

请你实现 Trie 类：

· Trie() 初始化前缀树对象。
· void insert(String word) 向前缀树中插入字符串 word 。
· boolean search(String word) 如果字符串 word 在前缀树中，返回 true（即，在检索之前已经插入）；否则，返回 false 。
· boolean startsWith(String prefix) 如果之前已经插入的字符串 word 的前缀之一为 prefix ，返回 true ；否则，返回 false 。

```
/// Trie（前缀树）数据结构
///
/// 用于高效存储和查询字符串集合，支持插入、搜索和前缀匹配操作
/// 时间复杂度：O(L)，其中L为字符串长度
/// 空间复杂度：O(N*L)，N为单词数，L为平均长度
struct Trie {
    root: Node,
}

/// Trie节点
struct Node {
    /// 标记是否为单词结尾
    is_end: bool,
    /// 子节点数组，使用Box指针实现动态分配
    children: [Option<Box<Node>>; 26],
}

impl Node {
    /// 创建新节点
    #[inline]
    fn new() -> Self {
        Node {
            is_end: false,
            children: Default::default(), // 初始化为26个None
        }
    }

    /// 将字符转换为数组索引（仅限小写字母）
    #[inline(always)]
    const fn char_to_idx(ch: char) -> usize {
        (ch as u8 - b'a') as usize
    }
}

impl Trie {
    /// 初始化Trie数据结构
    ///
    /// # 示例
    /// ```
    /// let trie = Trie::new();
    /// ```
    pub fn new() -> Self {
        Trie {
            root: Node::new(),
        }
    }

    /// 插入单词到Trie中
    ///
    /// # 参数
    /// * `word` - 要插入的单词（仅包含小写字母）
    ///
    /// # 示例
    /// ```
    /// let mut trie = Trie::new();
    /// trie.insert("hello".to_string());
    /// ```
    pub fn insert(&mut self, word: String) {
        let mut curr = &mut self.root;

        for ch in word.chars() {
            let idx = Node::char_to_idx(ch);
            // 如果子节点不存在则创建
            if curr.children[idx].is_none() {
                curr.children[idx] = Some(Box::new(Node::new()));
            }
            // 移动到子节点
            curr = curr.children[idx].as_mut().unwrap();
        }

        curr.is_end = true;
    }

    /// 搜索单词是否完整存在于Trie中
    ///
    /// # 参数
    /// * `word` - 要搜索的单词
    ///
    /// # 返回
    /// 如果单词存在返回true，否则返回false
    ///
    /// # 示例
    /// ```
    /// let mut trie = Trie::new();
    /// trie.insert("hello".to_string());
    /// assert!(trie.search("hello".to_string()));
    /// assert!(!trie.search("hell".to_string()));
    /// ```
    pub fn search(&self, word: String) -> bool {
        self.search_node(&word)
            .map(|node| node.is_end)
            .unwrap_or(false)
    }

    /// 检查是否存在以给定前缀开头的单词
    ///
    /// # 参数
    /// * `prefix` - 要检查的前缀
    ///
    /// # 返回
    /// 如果存在任何单词以该前缀开头返回true，否则返回false
    ///
    /// # 示例
    /// ```
    /// let mut trie = Trie::new();
    /// trie.insert("hello".to_string());
    /// assert!(trie.starts_with("he".to_string()));
    /// assert!(!trie.starts_with("xyz".to_string()));
    /// ```
    pub fn starts_with(&self, prefix: String) -> bool {
        self.search_node(&prefix).is_some()
    }

    /// 辅助函数：查找字符串对应的节点
    /// 如果路径存在则返回Some(&Node)，否则返回None
    #[inline]
    fn search_node(&self, s: &str) -> Option<&Node> {
        let mut curr = &self.root;

        for ch in s.chars() {
            let idx = Node::char_to_idx(ch);
            match &curr.children[idx] {
                Some(node) => curr = node,
                None => return None,
            }
        }

        Some(curr)
    }
}

impl Default for Trie {
    fn default() -> Self {
        Self::new()
    }
}

```
