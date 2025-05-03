---
title: "Leetcode 随机化2"
date: 2024-12-21T15:03:43+08:00
tags: ["leetcode", "随机化"]
draft: false
---

## O(1) 时间插入、删除和获取随机元素 - 允许重复

RandomizedCollection 是一种包含数字集合(可能是重复的)的数据结构。它应该支持插入和删除特定元素，以及删除随机元素。

实现 RandomizedCollection 类:

RandomizedCollection()初始化空的 RandomizedCollection 对象。
bool insert(int val) 将一个 val 项插入到集合中，即使该项已经存在。如果该项不存在，则返回 true ，否则返回 false 。
bool remove(int val) 如果存在，从集合中移除一个 val 项。如果该项存在，则返回 true ，否则返回 false 。注意，如果 val 在集合中出现多次，我们只删除其中一个。
int getRandom() 从当前的多个元素集合中返回一个随机元素。每个元素被返回的概率与集合中包含的相同值的数量 线性相关 。
您必须实现类的函数，使每个函数的 平均 时间复杂度为 O(1) 。

注意：生成测试用例时，只有在 RandomizedCollection 中 至少有一项 时，才会调用 getRandom 。

 

示例 1:

输入
["RandomizedCollection", "insert", "insert", "insert", "getRandom", "remove", "getRandom"]
[[], [1], [1], [2], [], [1], []]
输出
[null, true, false, true, 2, true, 1]

解释
RandomizedCollection collection = new RandomizedCollection();// 初始化一个空的集合。
collection.insert(1);   // 返回 true，因为集合不包含 1。
                        // 将 1 插入到集合中。
collection.insert(1);   // 返回 false，因为集合包含 1。
                        // 将另一个 1 插入到集合中。集合现在包含 [1,1]。
collection.insert(2);   // 返回 true，因为集合不包含 2。
                        // 将 2 插入到集合中。集合现在包含 [1,1,2]。
collection.getRandom(); // getRandom 应当:
                        // 有 2/3 的概率返回 1,
                        // 1/3 的概率返回 2。
collection.remove(1);   // 返回 true，因为集合包含 1。
                        // 从集合中移除 1。集合现在包含 [1,2]。
collection.getRandom(); // getRandom 应该返回 1 或 2，两者的可能性相同。

```
use std::collections::{HashMap, HashSet};
use rand::{thread_rng, Rng};

struct RandomizedCollection {
    vals: Vec<i32>,
    maps: HashMap<i32, HashSet<usize>>,
}

/**
 * `&self` means the method takes an immutable reference.
 * If you need a mutable reference, change it to `&mut self` instead.
 */
impl RandomizedCollection {
    fn new() -> Self {
        Self {
            vals: Vec::new(),
            maps: HashMap::new(),
        }
    }
    
    fn insert(&mut self, val: i32) -> bool {
        let idx = self.vals.len();
        let idxs = self.maps.entry(val).or_insert(HashSet::new());
        idxs.insert(idx);
        self.vals.push(val);
        idxs.len() == 1
    }
    
    fn remove(&mut self, val: i32) -> bool {
        match self.maps.get_mut(&val) {
            Some(idxs) if !idxs.is_empty() => {
                let idx = *idxs.iter().next().unwrap();
                idxs.remove(&idx);
                let pop_val = self.vals.pop().unwrap();
                let len = self.vals.len();
                if idx < len {
                    self.vals[idx] = pop_val;
                    self.maps.entry(pop_val).and_modify(|v| {
                        v.remove(&len);
                        v.insert(idx);
                    });
                }

                true
            }
            _ => false,
        }
    }
    
    fn get_random(&self) -> i32 {
        let random_idx = thread_rng().gen_range(0..self.vals.len());
        self.vals[random_idx]
    }
}

```