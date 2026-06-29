---
title: "leetcode-字典树37"
date: 2026-06-02T10:23:08+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 键值映射

实现一个 MapSum 类，支持两个方法，insert 和 sum：

· MapSum() 初始化 MapSum 对象

· void insert(String key, int val) 插入 key-val 键值对，字符串表示键 key ，整数表示值 val 。如果键 key 已经存在，那么原来的键值对将被替代成新的键值对。

· int sum(string prefix) 返回所有以该前缀 prefix 开头的键 key 的值的总和。


```
use std::collections::HashMap;

/// MapSum 数据结构
///
/// 支持两种操作：
/// 1. insert(key, val) - 插入键值对，如果键已存在则更新值
/// 2. sum(prefix) - 返回所有以给定前缀开头的键的值之和
///
/// # 示例
/// ```
/// let mut map_sum = MapSum::new();
/// map_sum.insert("apple".to_string(), 3);
/// assert_eq!(map_sum.sum("ap".to_string()), 3);
/// map_sum.insert("app".to_string(), 2);
/// assert_eq!(map_sum.sum("ap".to_string()), 5);
/// ```
struct MapSum {
    /// 当前节点的值（如果是单词结尾）
    val: i32,
    /// 当前节点子树的所有值的和（包括当前节点）
    sum: i32,
    /// 子节点数组，26个小写字母
    children: [Option<Box<MapSum>>; 26],
}

impl MapSum {
    /// 初始化 MapSum 数据结构
    fn new() -> Self {
        Self {
            val: 0,
            sum: 0,
            children: Default::default(), // 初始化为26个None
        }
    }

    /// 插入键值对
    ///
    /// # 参数
    /// * `key` - 键字符串
    /// * `val` - 值
    ///
    /// 如果键已存在，会更新值并调整所有前缀节点的和
    fn insert(&mut self, key: String, val: i32) {
        let mut node = self;
        let key_bytes = key.as_bytes();

        // 首先查找是否存在旧值
        let old_val = Self::find_value(node, key_bytes);
        let diff = val - old_val;

        // 如果值没有变化，直接返回
        if diff == 0 {
            return;
        }

        // 更新路径上所有节点的和
        for &byte in key_bytes {
            let idx = (byte - b'a') as usize;
            if node.children[idx].is_none() {
                node.children[idx] = Some(Box::new(MapSum::new()));
            }
            node.sum += diff;
            node = node.children[idx].as_mut().unwrap();
        }

        // 更新最后一个节点的值和和
        node.val = val;
        node.sum += diff;
    }

    /// 查找键对应的值
    fn find_value(node: &MapSum, key: &[u8]) -> i32 {
        let mut node = node;
        for &byte in key {
            let idx = (byte - b'a') as usize;
            match node.children[idx].as_ref() {
                Some(child) => node = child,
                None => return 0,
            }
        }
        node.val
    }

    /// 返回所有以给定前缀开头的键的值之和
    ///
    /// # 参数
    /// * `prefix` - 前缀字符串
    ///
    /// # 返回
    /// 所有匹配键的值之和
    fn sum(&self, prefix: String) -> i32 {
        let mut node = self;
        let prefix_bytes = prefix.as_bytes();

        // 找到前缀对应的节点
        for &byte in prefix_bytes {
            let idx = (byte - b'a') as usize;
            match node.children[idx].as_ref() {
                Some(child) => node = child,
                None => return 0,
            }
        }

        // 返回该节点记录的和
        node.sum
    }
}
```
