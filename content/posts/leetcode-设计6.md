---
title: "leetcode-设计6"
date: 2026-07-14T10:41:24+08:00
tags: ["leetcode", "设计"]
draft: false
---


## O(1) 时间插入、删除和获取随机元素

实现RandomizedSet 类：

RandomizedSet() 初始化 RandomizedSet 对象

bool insert(int val) 当元素 val 不存在时，向集合中插入该项，并返回 true ；否则，返回 false 。

bool remove(int val) 当元素 val 存在时，从集合中移除该项，并返回 true ；否则，返回 false 。

int getRandom() 随机返回现有集合中的一项（测试用例保证调用此方法时集合中至少存在一个元素）。每个元素应该有 相同的概率 被返回。

你必须实现类的所有函数，并满足每个函数的 平均 时间复杂度为 O(1) 。




```
use rand::Rng;
use std::collections::HashMap;

/// 支持 O(1) 插入、删除和随机访问的集合
struct RandomizedSet {
    /// 存储实际元素的数组，用于 O(1) 随机访问
    data: Vec<i32>,
    /// 元素值到其在 data 中索引的映射，用于 O(1) 查找
    index_map: HashMap<i32, usize>,
}

impl RandomizedSet {
    /// 创建一个空的 RandomizedSet
    pub fn new() -> Self {
        Self {
            data: Vec::with_capacity(16),
            index_map: HashMap::with_capacity(16),
        }
    }

    /// 插入元素，如果元素已存在返回 false，否则插入并返回 true
    ///
    /// 时间复杂度: O(1) 平均
    pub fn insert(&mut self, val: i32) -> bool {
        // 使用 entry API 避免双重查找
        if let std::collections::hash_map::Entry::Vacant(entry) = self.index_map.entry(val) {
            entry.insert(self.data.len());
            self.data.push(val);
            true
        } else {
            false
        }
    }

    /// 移除元素，如果元素存在返回 true，否则返回 false
    ///
    /// 实现策略：用最后一个元素替换被删除位置，保持数组紧凑
    /// 时间复杂度: O(1) 平均
    pub fn remove(&mut self, val: i32) -> bool {
        if let Some(&index) = self.index_map.get(&val) {
            let last_index = self.data.len() - 1;

            // 如果不是最后一个元素，用最后一个元素覆盖被删除位置
            if index != last_index {
                let last_val = self.data[last_index];
                self.data[index] = last_val;
                self.index_map.insert(last_val, index);
            }

            // 移除最后一个元素和映射
            self.data.pop();
            self.index_map.remove(&val);
            true
        } else {
            false
        }
    }

    /// 随机返回集合中的一个元素，每个元素被选中的概率相等
    ///
    /// 前提条件：集合非空（由测试保证）
    /// 时间复杂度: O(1)
    pub fn get_random(&self) -> i32 {
        let idx = rand::thread_rng().gen_range(0..self.data.len());
        self.data[idx]
    }
}
```
