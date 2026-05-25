---
title: "leetcode-哈希函数4"
date: 2026-04-26T20:59:32+08:00
tags: ["leetcode", "哈希函数"]
draft: false
---


## 设计哈希映射


不使用任何内建的哈希表库设计一个哈希映射（HashMap）。

实现 MyHashMap 类：

MyHashMap() 用空映射初始化对象
void put(int key, int value) 向 HashMap 插入一个键值对 (key, value) 。如果 key 已经存在于映射中，则更新其对应的值 value 。
int get(int key) 返回特定的 key 所映射的 value ；如果映射中不包含 key 的映射，返回 -1 。
void remove(key) 如果映射中存在 key 的映射，则移除 key 和它所对应的 value 。

```
use std::collections::LinkedList;

/// 使用分离链接法实现的简单哈希映射。
/// 采用固定数量的桶（默认 769 个），每个桶是一个链表，
/// 用于处理哈希冲突。
struct MyHashMap {
    /// 桶数组，每个桶是一个存储键值对的链表。
    buckets: Vec<LinkedList<(i32, i32)>>,
}

impl MyHashMap {
    /// 桶的数量，选择质数以减少哈希冲突。
    const NUM_BUCKETS: usize = 769;

    /// 创建一个新的空哈希映射。
    pub fn new() -> Self {
        Self {
            buckets: vec![LinkedList::new(); Self::NUM_BUCKETS],
        }
    }

    /// 插入或更新键值对。
    /// 如果键已存在，则更新其值；否则在对应桶的末尾插入。
    pub fn put(&mut self, key: i32, value: i32) {
        let bucket = &mut self.buckets[Self::bucket_index(key)];
        // 使用 find 方法查找并就地更新，避免显式循环
        if let Some(item) = bucket.iter_mut().find(|(k, _)| *k == key) {
            item.1 = value;
        } else {
            bucket.push_back((key, value));
        }
    }

    /// 获取键对应的值。
    /// 如果键不存在，返回 -1（遵循题目约定）。
    pub fn get(&self, key: i32) -> i32 {
        self.buckets[Self::bucket_index(key)]
            .iter()
            .find(|(k, _)| *k == key)
            .map(|&(_, v)| v)
            .unwrap_or(-1)
    }

    /// 从哈希映射中移除键及其对应的值（如果存在）。
    /// 使用 stable Rust 中可用的 split_off 和 append 方法实现，
    /// 避免使用不稳定的 retain 特性。
    pub fn remove(&mut self, key: i32) {
        let bucket = &mut self.buckets[Self::bucket_index(key)];
        // 查找要移除的元素在链表中的位置
        if let Some(idx) = bucket.iter().position(|(k, _)| *k == key) {
            // 根据位置选择最高效的移除方式
            if idx == 0 {
                // 移除头部：O(1)
                bucket.pop_front();
            } else if idx == bucket.len() - 1 {
                // 移除尾部：O(1)
                bucket.pop_back();
            } else {
                // 移除中间元素：使用 split_off 分割链表，再重新拼接
                // 将链表从 idx 处分割，此时 tail 包含自 idx 起的所有元素
                let mut tail = bucket.split_off(idx);
                // 丢弃 tail 的第一个元素（即要删除的目标）
                tail.pop_front();
                // 将剩余的 tail 拼接到原链表末尾
                bucket.append(&mut tail);
            }
        }
    }

    /// 辅助函数：计算给定键对应的桶索引。
    #[inline(always)]
    fn bucket_index(key: i32) -> usize {
        // Rust 的取模运算对负数也有效，结果会包含负余数，
        // 通过 as usize 转换后依然在有效范围内。
        (key % Self::NUM_BUCKETS as i32) as usize
    }
}

// 实现 Default trait，使 MyHashMap::default() 可用
impl Default for MyHashMap {
    fn default() -> Self {
        Self::new()
    }
}
```
