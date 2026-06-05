---
title: "leetcode-字典树2"
date: 2026-06-02T10:23:06+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 实现 Trie (前缀树)


Trie（发音类似 "try"）或者说 前缀树 是一种树形数据结构，用于高效地存储和检索字符串数据集中的键。这一数据结构有相当多的应用情景，例如自动补全和拼写检查。

请你实现 Trie 类：

Trie() 初始化前缀树对象。
void insert(String word) 向前缀树中插入字符串 word 。
boolean search(String word) 如果字符串 word 在前缀树中，返回 true（即，在检索之前已经插入）；否则，返回 false 。
boolean startsWith(String prefix) 如果之前已经插入的字符串 word 的前缀之一为 prefix ，返回 true ；否则，返回 false 。


```
/// 前缀树（Trie）的公共接口
/// 用于高效存储和查询字符串集合
#[derive(Default)]
pub struct Trie {
    root: Node, // 根节点，不存储任何字符
}

/// 前缀树的节点结构
#[derive(Default)]
struct Node {
    // 26 个字母的子节点（a-z）
    // Option 表示该字母路径是否存在
    // Box<Node> 将节点存储在堆上，避免递归结构导致的大小不确定问题
    children: [Option<Box<Node>>; 26],

    // 标记当前节点是否构成一个完整的单词
    is_word: bool,
}

impl Trie {
    /// 创建一个新的空前缀树
    ///
    /// # 示例
    /// ```
    /// let mut trie = Trie::new();
    /// trie.insert("hello".to_string());
    /// assert!(trie.search("hello".to_string()));
    /// ```
    pub fn new() -> Self {
        Self::default()
        // 手动实现的等效代码：
        // Trie {
        //     root: Node {
        //         children: [None; 26], // ❌ 这种写法要求数组元素实现 Copy trait
        //         is_word: false,
        //     }
        // }
        // 因此使用 #[derive(Default)] 自动生成默认实现更简洁
    }

    /// 向前缀树中插入一个单词
    ///
    /// # 参数
    /// - `word`: 要插入的单词（假设只包含小写字母 a-z）
    ///
    /// # 算法
    /// 遍历单词的每个字符，沿着对应路径向下走
    /// 如果路径不存在则创建新节点，最后在结尾节点标记 `is_word = true`
    pub fn insert(&mut self, word: String) {
        let mut node = &mut self.root;

        // 遍历单词的每个字节（这里假设都是小写 ASCII 字母）
        for c in word.as_bytes() {
            // 将字符转换为索引：'a' -> 0, 'b' -> 1, ..., 'z' -> 25
            let idx = (c - b'a') as usize;

            // 获取子节点的可变引用（或插入新节点）
            let next = &mut node.children[idx];

            // get_or_insert_with 的逻辑：
            // - 如果 next.is_some()，直接返回内部的可变引用
            // - 如果 next.is_none()，使用 Box::<Node>::default() 创建新节点，
            //   插入后再返回其可变引用
            node = next.get_or_insert_with(Box::<Node>::default);
        }

        // 标记最后一个节点为单词结尾
        node.is_word = true;
    }

    /// 查找单词是否完全存在于前缀树中
    ///
    /// # 参数
    /// - `word`: 要查找的单词
    ///
    /// # 返回
    /// - `true`: 单词存在
    /// - `false`: 单词不存在
    pub fn search(&self, word: String) -> bool {
        // 获取单词对应的节点，并检查该节点是否为单词结尾
        self.get_node(&word).map_or(false, |node| node.is_word)

        // 等效的 match 写法：
        // match self.get_node(&word) {
        //     Some(node) if node.is_word => true,
        //     _ => false
        // }
    }

    /// 检查是否存在以给定前缀开头的单词
    ///
    /// # 参数
    /// - `prefix`: 要查询的前缀
    ///
    /// # 返回
    /// - `true`: 至少有一个单词以此前缀开头
    /// - `false`: 没有单词以此前缀开头
    ///
    /// # 示例
    /// ```
    /// let mut trie = Trie::new();
    /// trie.insert("apple".to_string());
    /// assert!(trie.starts_with("app".to_string()));
    /// assert!(!trie.starts_with("apt".to_string()));
    /// ```
    pub fn starts_with(&self, prefix: String) -> bool {
        // 只要能找到前缀对应的节点即可，无需检查 is_word
        self.get_node(&prefix).is_some()
    }

    /// 辅助方法：获取字符串 `s` 对应的最后一个节点
    ///
    /// # 参数
    /// - `s`: 要查询的字符串（单词或前缀）
    ///
    /// # 返回
    /// - `Some(&Node)`: 字符串路径存在，返回最后一个字符对应的节点
    /// - `None`: 字符串路径不存在
    fn get_node(&self, s: &str) -> Option<&Node> {
        let mut node = &self.root;

        // 逐个字符向下查找
        for c in s.as_bytes() {
            let idx = (c - b'a') as usize;

            match &node.children[idx] {
                Some(next) => {
                    // 路径存在，继续向下
                    node = next.as_ref()
                },
                None => {
                    // 路径中断，返回 None
                    return None;
                }
            }
        }

        // 成功找到字符串对应的节点
        Some(node)
    }
}
```
