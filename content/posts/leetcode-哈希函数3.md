---
title: "leetcode-哈希函数3"
date: 2026-04-26T20:59:32+08:00
tags: ["leetcode", "哈希函数"]
draft: false
---


## 设计哈希集合

不使用任何内建的哈希表库设计一个哈希集合（HashSet）。

实现 MyHashSet 类：

void add(key) 向哈希集合中插入值 key 。
bool contains(key) 返回哈希集合中是否存在这个值 key 。
void remove(key) 将给定值 key 从哈希集合中删除。如果哈希集合中没有这个值，什么也不做。

```
/// 一个基于位图（bitset）实现的哈希集合，仅支持非负整数（i32 范围）。
/// 内部使用 `u64` 数组作为存储，每个 `u64` 位代表 64 个连续的键值。
///
/// 存储容量：默认覆盖 `[0, CAPACITY)`，其中 `CAPACITY = BITS_PER_WORD * WORD_COUNT`。
/// 当前配置可安全容纳 `0 ~ 1,000,063` 的键。
struct MyHashSet {
    /// 位图存储：每个 `u64` 是一个字（word），字的第 `b` 位（0-based）表示键 `word_index * 64 + b`。
    words: Vec<u64>,
}

impl MyHashSet {
    /// 每个字（word）包含的位数。
    const BITS_PER_WORD: usize = 64;
    /// 为了使容量至少达到 1e6，需要的字数 = ceil(1_000_000 / 64) = 15625，再加一个额外字确保安全。
    const WORD_COUNT: usize = 15626; // 15626 * 64 = 1,000,064

    /// 创建一个新的空集合。
    pub fn new() -> Self {
        Self {
            words: vec![0; Self::WORD_COUNT],
        }
    }

    /// 将 `key` 插入集合。
    /// # Panics
    /// 如果 `key` 为负或超出预分配容量（≥ `BITS_PER_WORD * WORD_COUNT`），则会因数组越界而 panic。
    pub fn add(&mut self, key: i32) {
        let (word_idx, bit_mask) = Self::locate(key);
        self.words[word_idx] |= bit_mask;
    }

    /// 从集合中移除 `key`（如果存在）。
    pub fn remove(&mut self, key: i32) {
        let (word_idx, bit_mask) = Self::locate(key);
        self.words[word_idx] &= !bit_mask;
    }

    /// 检查 `key` 是否存在于集合中。
    pub fn contains(&self, key: i32) -> bool {
        let (word_idx, bit_mask) = Self::locate(key);
        (self.words[word_idx] & bit_mask) != 0
    }

    /// 辅助函数：根据键值计算所在字索引和对应位的掩码。
    /// 采用函数式风格，返回一个元组，便于组合使用。
    #[inline]
    fn locate(key: i32) -> (usize, u64) {
        let key = key as usize;       // 将 i32 转为非负索引（调用者需保证 key >= 0）
        let word_idx = key / Self::BITS_PER_WORD;
        let bit_offset = key % Self::BITS_PER_WORD;
        let bit_mask = 1u64 << bit_offset;
        (word_idx, bit_mask)
    }
}

// 实现 Default trait，允许 `MyHashSet::default()`
impl Default for MyHashSet {
    fn default() -> Self {
        Self::new()
    }
}
```


1.消除魔数

提取 BITS_PER_WORD = 64 和 WORD_COUNT = 15626 为关联常量，并注释其由来（覆盖 0 ~ 1,000,063）。
原始代码中的 15626 和 64 现在具有明确语义。

2.位索引计算函数化

新增 locate 辅助方法（#[inline] 保证零开销），返回 (word_index, bit_mask) 元组。
三个公共方法均调用该函数，消除重复的除法和取模逻辑，提高可维护性。

3.安全性考虑

原代码对负数 key 会因 as usize 转换产生极大值（内存越界），现通过文档注释明确调用者需保证 key >= 0。
保留了越界 panic 行为（与原始相同）。
