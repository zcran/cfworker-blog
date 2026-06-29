---
title: "leetcode-字典树35"
date: 2026-06-02T10:23:08+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 单词替换

在英语中，有一个叫做 词根(root) 的概念，它可以跟着其他一些词组成另一个较长的单词——我们称这个词为 继承词(successor)。例如，词根an，跟随着单词 other(其他)，可以形成新的单词 another(另一个)。

现在，给定一个由许多词根组成的词典和一个句子，需要将句子中的所有继承词用词根替换掉。如果继承词有许多可以形成它的词根，则用最短的词根替换它。

需要输出替换之后的句子。

```
impl Solution {
    /// 使用词典替换句子中的单词
    ///
    /// 将句子中的每个单词替换为词典中最短的前缀匹配
    ///
    /// # 参数
    /// * `dictionary` - 词典，包含用于替换的词根
    /// * `sentence` - 原始句子，单词之间用空格分隔
    ///
    /// # 返回
    /// 替换后的新句子
    pub fn replace_words(dictionary: Vec<String>, sentence: String) -> String {
        let mut trie = Trie::new();

        // 插入所有词典单词
        for dict in dictionary {
            trie.insert(&dict);
        }

        // 处理每个单词并收集结果
        sentence
            .split_whitespace()  // 使用 split_whitespace() 更高效且处理多个空格
            .map(|word| trie.prefix(word))
            .collect::<Vec<String>>()
            .join(" ")
    }
}

/// Trie（前缀树）节点
#[derive(Default)]
struct Trie {
    /// 子节点数组，使用Box实现动态分配
    children: [Option<Box<Trie>>; 26],
    /// 标记当前节点是否为单词结尾
    is_end: bool,
}

impl Trie {
    /// 创建新的Trie实例
    pub fn new() -> Self {
        Default::default()
    }

    /// 插入单词到Trie中
    ///
    /// # 参数
    /// * `word` - 要插入的单词（仅包含小写字母）
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

    /// 查找单词的最短前缀匹配
    ///
    /// # 参数
    /// * `word` - 要查找的单词
    ///
    /// # 返回
    /// 如果找到匹配的前缀，返回该前缀；否则返回原单词
    pub fn prefix(&self, word: &str) -> String {
        let mut node = self;
        let mut prefix_len = 0;

        for (i, &byte) in word.as_bytes().iter().enumerate() {
            let idx = (byte - b'a') as usize;

            match node.children[idx].as_ref() {
                Some(child) => {
                    prefix_len = i + 1;
                    if child.is_end {
                        return word[..prefix_len].to_string();
                    }
                    node = child;
                }
                None => break,
            }
        }

        // 没有找到匹配的前缀，返回原单词
        word.to_string()
    }
}
```
