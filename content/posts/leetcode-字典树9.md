---
title: "leetcode-字典树9"
date: 2026-06-02T10:23:07+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 单词替换


在英语中，我们有一个叫做 词根(root) 的概念，可以词根 后面 添加其他一些词组成另一个较长的单词——我们称这个词为 衍生词 (derivative)。例如，词根 help，跟随着 继承词 "ful"，可以形成新的单词 "helpful"。

现在，给定一个由许多 词根 组成的词典 dictionary 和一个用空格分隔单词形成的句子 sentence。你需要将句子中的所有 衍生词 用 词根 替换掉。如果 衍生词 有许多可以形成它的 词根，则用 最短 的 词根 替换它。

你需要输出替换之后的句子。

```
impl Solution {
    /// 使用词典替换句子中的单词
    ///
    /// 规则：如果句子中的单词有前缀在词典中，则替换为该前缀（最短匹配）
    ///
    /// # 例子
    /// ```
    /// let dict = vec!["cat".to_string(), "bat".to_string(), "rat".to_string()];
    /// let sentence = "the cattle was rattled by the battery".to_string();
    /// assert_eq!(
    ///     Solution::replace_words(dict, sentence),
    ///     "the cat was rat by the bat"
    /// );
    /// ```
    pub fn replace_words(dictionary: Vec<String>, sentence: String) -> String {
        // 构建前缀树
        let mut trie = Trie::new();
        for dict in dictionary {
            trie.insert(&dict);
        }

        // 替换每个单词并收集结果
        sentence
            .split_whitespace()  // 比 split(" ") 更通用（处理多个空格）
            .map(|word| trie.prefix(word))
            .collect::<Vec<_>>()
            .join(" ")
    }
}

/// 前缀树（Trie）节点
#[derive(Default)]
struct Trie {
    /// 26 个子节点（a-z），只存储实际存在的分支
    children: [Option<Box<Trie>>; 26],
    /// 标记当前节点是否为某个词典单词的结尾
    is_end: bool,
}

impl Trie {
    /// 创建一个新的前缀树
    pub fn new() -> Self {
        Default::default()
    }

    /// 插入单词
    ///
    /// # 复杂度
    /// - 时间：O(L)，L 为单词长度
    /// - 空间：O(L) 最坏情况
    pub fn insert(&mut self, word: &str) {
        let mut node = self;

        for &byte in word.as_bytes() {
            let idx = (byte - b'a') as usize;
            node = node.children[idx]
                .get_or_insert_with(|| Box::new(Trie::new()))
                .as_mut();
        }

        node.is_end = true;
    }

    /// 查找单词的最短前缀（如果存在）
    ///
    /// # 返回
    /// - 如果在词典中找到前缀，返回该前缀
    /// - 否则返回原单词
    ///
    /// # 复杂度
    /// - 时间：O(L)，L 为单词长度
    /// - 空间：O(L) 用于构建结果字符串
    pub fn prefix(&self, word: &str) -> String {
        let mut node = self;
        let mut result = String::with_capacity(word.len());  // 预分配容量

        for &byte in word.as_bytes() {
            let idx = (byte - b'a') as usize;
            let ch = byte as char;

            // 将当前字符加入结果
            result.push(ch);

            match &node.children[idx] {
                Some(child) => {
                    // 如果当前节点是单词结尾，返回已构建的前缀
                    if child.is_end {
                        return result;
                    }
                    node = child;
                }
                None => break,  // 路径中断，无法继续匹配
            }
        }

        // 没有找到匹配的前缀，返回原单词
        word.to_string()
    }
}
```
