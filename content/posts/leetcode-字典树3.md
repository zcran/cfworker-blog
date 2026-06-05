---
title: "leetcode-字典树3"
date: 2026-06-02T10:23:06+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 添加与搜索单词 - 数据结构设计


请你设计一个数据结构，支持 添加新单词 和 查找字符串是否与任何先前添加的字符串匹配 。

实现词典类 WordDictionary ：

WordDictionary() 初始化词典对象
void addWord(word) 将 word 添加到数据结构中，之后可以对它进行匹配
bool search(word) 如果数据结构中存在字符串与 word 匹配，则返回 true ；否则，返回  false 。word 中可能包含一些 '.' ，每个 . 都可以表示任何一个字母。

```
use std::collections::HashMap;

/// 字典树的节点结构（支持通配符 '.' 匹配任意字母）
#[derive(Default)]
struct Trie {
    /// 标记当前节点是否为一个完整单词的结尾
    is_end: bool,

    /// 子节点映射：字母索引（0-25）-> 子节点
    /// 使用 HashMap 而不是固定数组，更节省内存（只存储实际存在的分支）
    child: HashMap<usize, Box<Trie>>,
}

impl Trie {
    /// 创建一个新的 Trie 节点
    fn new() -> Self {
        Default::default()
    }

    /// 递归搜索：支持 '.' 通配符匹配任意一个字母
    ///
    /// # 参数
    /// - `chars`: 要搜索的单词（字符数组形式）
    ///
    /// # 返回
    /// - `true`: 单词存在于字典树中
    /// - `false`: 单词不存在
    ///
    /// # 算法核心
    /// 1. 普通字母：精确匹配，继续向下搜索
    /// 2. 通配符 '.'：尝试所有可能的分支，只要有一个成功就返回 true
    fn search(&self, chars: &[char]) -> bool {
        let mut cur = self;

        // 遍历单词的每个字符位置
        for id in 0..chars.len() {
            // 情况1：当前字符是普通字母（a-z）
            if chars[id] != '.' {
                // 将字符转换为索引（'a' -> 0, 'b' -> 1, ...）
                let idx = chars[id] as usize - 'a' as usize;

                // 尝试获取对应的子节点
                if let Some(next_node) = cur.child.get(&idx) {
                    // 路径存在，继续向下搜索
                    cur = next_node;
                    continue;
                }

                // 路径不存在，匹配失败
                return false;
            }
            // 情况2：当前字符是通配符 '.'（匹配任意一个字母）
            else {
                // 遍历当前节点的所有子节点
                for (_, child_node) in cur.child.iter() {
                    // 递归搜索剩余部分（从下一个字符开始）
                    // 只要有一个分支能匹配成功，就返回 true
                    if child_node.search(&chars[id + 1..]) {
                        return true;
                    }
                }
                // 所有分支都失败
                return false;
            }
        }

        // 所有字符都匹配完成，返回当前节点是否为单词结尾
        cur.is_end
    }
}

/// 单词字典结构
/// 支持：
/// 1. 添加单词
/// 2. 搜索单词（支持 '.' 通配符）
pub struct WordDictionary {
    /// 字典树的根节点（使用 Box 存储在堆上）
    root: Box<Trie>,
}

impl WordDictionary {
    /// 创建一个新的单词字典
    ///
    /// # 示例
    /// ```
    /// let mut dict = WordDictionary::new();
    /// dict.add_word("bad".to_string());
    /// dict.add_word("dad".to_string());
    /// dict.add_word("mad".to_string());
    ///
    /// assert!(!dict.search("pad".to_string()));  // false
    /// assert!(dict.search("bad".to_string()));   // true
    /// assert!(dict.search(".ad".to_string()));   // true (匹配 bad, dad, mad)
    /// assert!(dict.search("b..".to_string()));   // true (匹配 bad)
    /// ```
    pub fn new() -> Self {
        WordDictionary {
            root: Box::new(Trie::new())
        }
    }

    /// 向字典中添加一个单词
    ///
    /// # 参数
    /// - `word`: 要添加的单词（只包含小写字母 a-z）
    ///
    /// # 算法
    /// 遍历单词的每个字母，沿着字典树向下走
    /// 如果路径不存在则创建新节点，最后标记结尾节点
    pub fn add_word(&mut self, word: String) {
        let mut cur = self.root.as_mut();

        // 遍历单词的每个字符，转换为索引（0-25）
        for idx in word.chars().map(|ch| (ch as u8 - b'a') as usize) {
            // entry API 的作用：
            // - 如果索引对应的子节点存在，直接返回其可变引用
            // - 如果不存在，使用 or_insert_with 创建新节点并返回其可变引用
            cur = cur.child.entry(idx).or_insert_with(|| Box::new(Trie::new()));
        }

        // 标记最后一个节点为单词结尾
        cur.is_end = true;
    }

    /// 搜索单词是否存在于字典中
    ///
    /// # 参数
    /// - `word`: 要搜索的单词（可以包含 '.' 通配符）
    ///
    /// # 返回
    /// - `true`: 单词存在于字典中
    /// - `false`: 单词不存在
    ///
    /// # 通配符说明
    /// '.' 可以匹配任意一个字母（a-z）
    /// 例如：".ad" 可以匹配 "bad", "dad", "mad" 等
    pub fn search(&self, word: String) -> bool {
        // 将字符串转换为字符数组，便于索引操作
        let chars: Vec<char> = word.chars().collect();
        // 调用 Trie 的递归搜索方法
        self.root.search(&chars[..])
    }
}
```
