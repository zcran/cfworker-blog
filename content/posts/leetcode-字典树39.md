---
title: "leetcode-字典树39"
date: 2026-06-02T10:23:08+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 单词频率


设计一个方法，找出任意指定单词在一本书中的出现频率。

你的实现应该支持如下操作：

WordsFrequency(book)构造函数，参数为字符串数组构成的一本书

get(word)查询指定单词在书中出现的频率


```
use std::collections::HashMap;

/// 单词频率统计器
///
/// 用于统计给定书册中每个单词出现的频率，并支持快速查询
///
/// # 示例
/// ```
/// let book = vec!["hello".to_string(), "world".to_string(), "hello".to_string()];
/// let words_freq = WordsFrequency::new(book);
/// assert_eq!(words_freq.get("hello".to_string()), 2);
/// assert_eq!(words_freq.get("world".to_string()), 1);
/// assert_eq!(words_freq.get("rust".to_string()), 0);
/// ```
struct WordsFrequency {
    /// 存储单词到频率的映射
    map: HashMap<String, i32>,
}

impl WordsFrequency {
    /// 创建新的单词频率统计器
    ///
    /// # 参数
    /// * `book` - 单词列表（书册内容）
    ///
    /// # 返回
    /// 初始化好的 WordsFrequency 实例
    ///
    /// # 复杂度
    /// * 时间: O(n)，其中 n 为单词总数
    /// * 空间: O(m)，其中 m 为不同单词的数量
    fn new(book: Vec<String>) -> Self {
        let mut map = HashMap::with_capacity(book.len());

        for word in book {
            // 使用 entry API 更简洁高效
            *map.entry(word).or_insert(0) += 1;
        }

        WordsFrequency { map }
    }

    /// 获取单词出现的频率
    ///
    /// # 参数
    /// * `word` - 要查询的单词
    ///
    /// # 返回
    /// 单词在书中出现的次数，如果未出现则返回 0
    ///
    /// # 复杂度
    /// * 时间: O(1) 平均情况
    /// * 空间: O(1)
    fn get(&self, word: String) -> i32 {
        // 使用 get 方法返回 Option<&i32>，解包后返回
        *self.map.get(&word).unwrap_or(&0)
    }
}

/// 改进版本：使用 &str 避免不必要的 String 分配
impl WordsFrequency {
    /// 使用字符串引用的 get 方法（更高效）
    ///
    /// # 参数
    /// * `word` - 要查询的单词的引用
    ///
    /// # 返回
    /// 单词出现的次数
    fn get_ref(&self, word: &str) -> i32 {
        *self.map.get(word).unwrap_or(&0)
    }
}

/// 使用数组存储的极简版本（仅限小写字母单词）
struct WordsFrequencyArray {
    map: HashMap<[u8; 26], i32>,  // 使用字符计数数组作为键
}

impl WordsFrequencyArray {
    fn new(book: Vec<String>) -> Self {
        let mut map = HashMap::new();

        for word in book {
            let mut key = [0u8; 26];
            for ch in word.chars() {
                if ch.is_ascii_lowercase() {
                    key[(ch as u8 - b'a') as usize] += 1;
                }
            }
            *map.entry(key).or_insert(0) += 1;
        }

        WordsFrequencyArray { map }
    }

    fn get(&self, word: &str) -> i32 {
        let mut key = [0u8; 26];
        for ch in word.chars() {
            if ch.is_ascii_lowercase() {
                key[(ch as u8 - b'a') as usize] += 1;
            }
        }
        *self.map.get(&key).unwrap_or(&0)
    }
}
```
