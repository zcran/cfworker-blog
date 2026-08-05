---
title: "leetcode-设计7"
date: 2026-07-14T10:41:24+08:00
tags: ["leetcode", "设计"]
draft: false
---


## O(1) 时间插入、删除和获取随机元素 - 允许重复

RandomizedCollection 是一种包含数字集合(可能是重复的)的数据结构。它应该支持插入和删除特定元素，以及删除随机元素。

实现 RandomizedCollection 类:

· RandomizedCollection()初始化空的 RandomizedCollection 对象。
· bool insert(int val) 将一个 val 项插入到集合中，即使该项已经存在。如果该项不存在，则返回 true ，否则返回 false 。
· bool remove(int val) 如果存在，从集合中移除一个 val 项。如果该项存在，则返回 true ，否则返回 false 。注意，如果 val 在集合中出现多次，我们只删除其中一个。
· int getRandom() 从当前的多个元素集合中返回一个随机元素。每个元素被返回的概率与集合中包含的相同值的数量 线性相关 。

您必须实现类的函数，使每个函数的 平均 时间复杂度为 O(1) 。

注意：生成测试用例时，只有在 RandomizedCollection 中 至少有一项 时，才会调用 getRandom 。


```
use rand::Rng;
use std::collections::{HashMap, HashSet};

/// 支持重复元素、O(1) 插入删除和随机访问的集合
struct RandomizedCollection {
    /// 存储实际元素，用于 O(1) 随机访问
    data: Vec<i32>,
    /// 元素值到其所有索引集合的映射，支持重复元素
    indices: HashMap<i32, HashSet<usize>>,
}

impl RandomizedCollection {
    /// 创建一个空的 RandomizedCollection
    pub fn new() -> Self {
        Self {
            data: Vec::with_capacity(16),
            indices: HashMap::with_capacity(16),
        }
    }

    /// 插入元素，如果该元素之前不存在则返回 true，否则返回 false
    ///
    /// 时间复杂度: O(1) 平均
    pub fn insert(&mut self, val: i32) -> bool {
        let is_new = !self.indices.contains_key(&val);

        // 记录新元素的索引位置
        self.indices
            .entry(val)
            .or_insert_with(HashSet::new)
            .insert(self.data.len());
        self.data.push(val);

        is_new
    }

    /// 删除一个元素，如果元素存在则返回 true，否则返回 false
    ///
    /// 实现策略：用最后一个元素替换被删除位置，保持数组紧凑
    /// 时间复杂度: O(1) 平均
    pub fn remove(&mut self, val: i32) -> bool {
        // 检查元素是否存在并获取索引
        let remove_idx = match self.indices.get(&val) {
            Some(set) => {
                if set.is_empty() {
                    return false;
                }
                *set.iter().next().unwrap()
            }
            None => return false,
        };

        let last_idx = self.data.len() - 1;

        // 删除索引记录
        self.indices.get_mut(&val).unwrap().remove(&remove_idx);

        // 如果不是最后一个元素，用最后一个元素覆盖被删除位置
        if remove_idx != last_idx {
            let last_val = self.data[last_idx];
            self.data[remove_idx] = last_val;

            // 更新最后一个元素的索引
            let last_set = self.indices.get_mut(&last_val).unwrap();
            last_set.remove(&last_idx);
            last_set.insert(remove_idx);
        }

        // 移除最后一个元素
        self.data.pop();

        // 如果该值没有剩余索引，删除整个条目
        if let Some(set) = self.indices.get(&val) {
            if set.is_empty() {
                self.indices.remove(&val);
            }
        }

        true
    }

    /// 随机返回集合中的一个元素，每个元素被选中的概率与其出现次数成正比
    ///
    /// 前提条件：集合非空（由测试保证）
    /// 时间复杂度: O(1)
    pub fn get_random(&self) -> i32 {
        let idx = rand::thread_rng().gen_range(0..self.data.len());
        self.data[idx]
    }
}
```
