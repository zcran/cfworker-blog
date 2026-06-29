---
title: "leetcode-字典树31"
date: 2026-06-02T10:23:08+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 最长公共后缀查询


给你两个字符串数组 wordsContainer 和 wordsQuery 。

对于每个 wordsQuery[i] ，你需要从 wordsContainer 中找到一个与 wordsQuery[i] 有 最长公共后缀 的字符串。如果 wordsContainer 中有两个或者更多字符串有最长公共后缀，那么答案为长度 最短 的。如果有超过两个字符串有 相同 最短长度，那么答案为它们在 wordsContainer 中出现 更早 的一个。

请你返回一个整数数组 ans ，其中 ans[i]是 wordsContainer中与 wordsQuery[i] 有 最长公共后缀 字符串的下标。

```
impl Solution {
    /// 为每个查询找出wordsContainer中与其最长公共后缀的字符串索引
    ///
    /// # 参数
    /// * `words_container` - 容器字符串数组
    /// * `words_query` - 查询字符串数组
    ///
    /// # 返回
    /// 每个查询对应的答案索引数组
    ///
    /// # 复杂度
    /// * 时间: O(L)，L为所有字符串总长度
    /// * 空间: O(L)，L为所有字符串总长度
    pub fn string_indices(words_container: Vec<String>, words_query: Vec<String>) -> Vec<i32> {
        // 预分配容量：根节点 + 所有字符总数（最坏情况每个字符一个新节点）
        let cap = 1 + words_container.iter().map(|s| s.len()).sum::<usize>();
        let mut trie = Trie::with_capacity(cap);

        // 插入所有容器字符串，记录原始索引
        for (idx, word) in words_container.iter().enumerate() {
            trie.insert(word.as_bytes(), idx as u32);
        }

        // 查询所有查询字符串
        words_query
            .iter()
            .map(|word| trie.query(word.as_bytes()) as i32)
            .collect()
    }
}

/// 字典树节点，使用 u32 索引实现紧凑存储
struct Node {
    /// 子节点索引数组，0 表示不存在（因为根节点索引为 0）
    children: [u32; 26],
    /// 经过该节点的最佳字符串索引（长度最短，索引最小）
    best_idx: u32,
    /// 最佳字符串的长度，用于比较
    best_len: u32,
}

impl Node {
    #[inline(always)]
    fn new(idx: u32, len: u32) -> Self {
        Self {
            children: [0; 26],
            best_idx: idx,
            best_len: len,
        }
    }
}

/// 字典树，使用连续内存 arena 实现
struct Trie {
    arena: Vec<Node>,
}

impl Trie {
    /// 创建指定容量的字典树
    #[inline]
    fn with_capacity(cap: usize) -> Self {
        let mut arena = Vec::with_capacity(cap);
        // 根节点使用最大索引和长度，确保会被第一个插入的字符串更新
        arena.push(Node::new(u32::MAX, u32::MAX));
        Self { arena }
    }

    /// 插入字符串的反转形式（将后缀问题转为前缀问题）
    ///
    /// # 参数
    /// * `bytes` - 字符串的字节切片（原始顺序）
    /// * `idx` - 该字符串在 words_container 中的索引
    #[inline]
    fn insert(&mut self, bytes: &[u8], idx: u32) {
        let len = bytes.len() as u32;
        let mut cur = 0;

        // 更新根节点信息
        if len < self.arena[cur].best_len {
            self.arena[cur].best_idx = idx;
            self.arena[cur].best_len = len;
        }

        // 从后往前遍历字符（实现反转）
        for &b in bytes.iter().rev() {
            let i = (b - b'a') as usize;
            let next = self.arena[cur].children[i];

            if next != 0 {
                // 子节点已存在，直接移动
                cur = next as usize;
            } else {
                // 创建新节点
                let new_node = Node::new(idx, len);
                let new_idx = self.arena.len() as u32;
                self.arena.push(new_node);
                self.arena[cur].children[i] = new_idx;
                cur = new_idx as usize;
            }

            // 更新当前节点的最佳信息
            let node = &mut self.arena[cur];
            if len < node.best_len {
                node.best_idx = idx;
                node.best_len = len;
            }
        }
    }

    /// 查询与给定字符串有最长公共后缀的容器字符串索引
    ///
    /// # 参数
    /// * `bytes` - 查询字符串的字节切片（原始顺序）
    ///
    /// # 返回
    /// 满足条件的字符串在 words_container 中的索引
    #[inline]
    fn query(&self, bytes: &[u8]) -> u32 {
        let mut cur = 0;

        // 从后往前遍历字符，尽可能深地匹配
        for &b in bytes.iter().rev() {
            let i = (b - b'a') as usize;
            let next = self.arena[cur].children[i];

            if next != 0 {
                cur = next as usize;
            } else {
                // 无法继续匹配，返回当前节点的最佳结果
                return self.arena[cur].best_idx;
            }
        }

        // 完全匹配，返回最后节点的最佳结果
        self.arena[cur].best_idx
    }
}
```
