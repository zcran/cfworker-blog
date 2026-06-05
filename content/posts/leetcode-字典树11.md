---
title: "leetcode-字典树11"
date: 2026-06-02T10:23:07+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 键值映射

设计一个 map ，满足以下几点:

· 字符串表示键，整数表示值
· 返回具有前缀等于给定字符串的键的值的总和

实现一个 MapSum 类：

· MapSum() 初始化 MapSum 对象
· void insert(String key, int val) 插入 key-val 键值对，字符串表示键 key ，整数表示值 val 。如果键 key 已经存在，那么原来的键值对 key-value 将被替代成新的键值对。
· int sum(string prefix) 返回所有以该前缀 prefix 开头的键 key 的值的总和。

```
use std::collections::{HashMap, HashSet};
use std::collections::hash_map::DefaultHasher;
use std::hash::{Hash, Hasher};

/// MapSum 数据结构 - 使用哈希映射实现
///
/// # 功能
/// - 插入键值对（key, val）
/// - 返回所有以给定前缀开头的键的值的总和
///
/// # 算法思路
/// 使用两个哈希表：
/// 1. `d` (data): 存储键的哈希值 -> 值
/// 2. `p` (prefix): 存储前缀的哈希值 -> 该前缀下所有完整键的哈希值集合
///
/// # 复杂度
/// - 插入: O(L) - L 为键的长度
/// - 求和: O(K) - K 为匹配前缀的键的数量
///
/// # 示例
/// ```
/// let mut map_sum = MapSum::new();
/// map_sum.insert("apple".to_string(), 3);
/// assert_eq!(map_sum.sum("ap".to_string()), 3);
/// map_sum.insert("app".to_string(), 2);
/// assert_eq!(map_sum.sum("ap".to_string()), 5);
/// ```
#[derive(Default)]
struct MapSum {
    /// 存储完整键的哈希值 -> 对应的值
    /// 使用 u64 哈希值作为键，避免存储完整字符串
    d: HashMap<u64, i32>,

    /// 存储前缀的哈希值 -> 该前缀下所有完整键的哈希值集合
    /// 例如：前缀 "ap" 对应 {"apple", "app"} 的哈希值
    p: HashMap<u64, HashSet<u64>>,
}

impl MapSum {
    /// 创建一个新的 MapSum 实例
    pub fn new() -> Self {
        Default::default()
    }

    /// 插入键值对
    ///
    /// # 参数
    /// - `key`: 字符串键
    /// - `val`: 对应的整数值
    ///
    /// # 算法
    /// 1. 计算完整键的哈希值
    /// 2. 存储键值对到 `d` 哈希表
    /// 3. 遍历键的所有前缀（包括完整键本身）
    /// 4. 将完整键的哈希值添加到每个前缀的集合中
    ///
    /// # 时间复杂度
    /// O(L)，其中 L 为键的长度
    pub fn insert(&mut self, key: String, val: i32) {
        // 计算完整键的哈希值
        let key_hash = hash(&key);

        // 存储键值对
        self.d.insert(key_hash, val);

        // 为每个前缀建立索引
        // 例如：key = "apple"
        // 前缀包括: "a", "ap", "app", "appl", "apple"
        for i in 1..=key.len() {
            let prefix_hash = hash(&key[..i]);  // 计算前缀的哈希值
            // 将完整键的哈希值添加到该前缀的集合中
            self.p.entry(prefix_hash).or_default().insert(key_hash);
        }
    }

    /// 返回所有以给定前缀开头的键的值的总和
    ///
    /// # 参数
    /// - `prefix`: 要查询的前缀字符串
    ///
    /// # 返回
    /// 所有匹配前缀的键的值的总和
    ///
    /// # 算法
    /// 1. 计算前缀的哈希值
    /// 2. 获取该前缀下的所有完整键的哈希值集合
    /// 3. 遍历集合，从 `d` 中取出对应的值并求和
    ///
    /// # 时间复杂度
    /// O(K)，其中 K 为匹配前缀的键的数量
    pub fn sum(&self, prefix: String) -> i32 {
        let prefix_hash = hash(&prefix);

        // 获取前缀对应的键集合，如果不存在则返回空集合
        self.p
            .get(&prefix_hash)
            .unwrap_or(&HashSet::new())
            .iter()
            .map(|key_hash| self.d[key_hash])  // 从数据哈希表中获取值
            .sum::<i32>()  // 求和
    }
}

/// 计算任意可哈希类型的哈希值
///
/// # 参数
/// - `t`: 任何实现了 `Hash` trait 的类型
///
/// # 返回
/// 64 位哈希值（u64）
///
/// # 示例
/// ```
/// let h1 = hash("hello");
/// let h2 = hash("world");
/// assert_ne!(h1, h2);
/// ```
fn hash<T: Hash>(t: T) -> u64 {
    let mut hasher = DefaultHasher::new();  // 创建默认哈希器
    t.hash(&mut hasher);                     // 计算哈希
    hasher.finish()                          // 返回哈希值
}
```
