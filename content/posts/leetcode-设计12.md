---
title: "leetcode-设计12"
date: 2026-07-14T10:41:25+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 基于时间的键值存储

设计一个基于时间的键值数据结构，该结构可以在不同时间戳存储对应同一个键的多个值，并针对特定时间戳检索键对应的值。

实现 TimeMap 类：

TimeMap() 初始化数据结构对象

void set(String key, String value, int timestamp) 存储给定时间戳 timestamp 时的键 key 和值 value。

String get(String key, int timestamp) 返回一个值，该值在之前调用了 set，其中 timestamp_prev <= timestamp 。如果有多个这样的值，它将返回与最大  timestamp_prev 关联的值。如果没有值，则返回空字符串（""）。


```
use std::collections::{BTreeMap, HashMap};

/// 基于时间的键值存储，支持按时间戳查询历史值
struct TimeMap {
    /// 键 -> (时间戳 -> 值) 的映射
    /// 使用 BTreeMap 保证时间戳有序，便于范围查询
    store: HashMap<String, BTreeMap<i32, String>>,
}

impl TimeMap {
    /// 创建一个空的 TimeMap
    pub fn new() -> Self {
        Self {
            store: HashMap::new(),
        }
    }

    /// 存储键值对及其时间戳
    ///
    /// 时间复杂度: O(log n)，n 为该键下的时间戳数量
    pub fn set(&mut self, key: String, value: String, timestamp: i32) {
        self.store
            .entry(key)
            .or_insert_with(BTreeMap::new)
            .insert(timestamp, value);
    }

    /// 获取指定键在给定时间戳（含）之前的最新值
    ///
    /// 返回时间戳 <= timestamp 的最大时间戳对应的值
    /// 如果不存在，返回空字符串
    ///
    /// 时间复杂度: O(log n)，n 为该键下的时间戳数量
    pub fn get(&self, key: String, timestamp: i32) -> String {
        self.store
            .get(&key)
            .and_then(|timestamps| timestamps.range(..=timestamp).next_back())
            .map(|(_, value)| value.clone())
            .unwrap_or_else(String::new)
    }
}
```
