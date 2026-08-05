---
title: "leetcode-设计31"
date: 2026-07-14T10:41:26+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 频率跟踪器

请你设计并实现一个能够对其中的值进行跟踪的数据结构，并支持对频率相关查询进行应答。

实现 FrequencyTracker 类：

FrequencyTracker()：使用一个空数组初始化 FrequencyTracker 对象。
void add(int number)：添加一个 number 到数据结构中。
void deleteOne(int number)：从数据结构中删除一个 number 。数据结构 可能不包含 number ，在这种情况下不删除任何内容。
bool hasFrequency(int frequency): 如果数据结构中存在出现 frequency 次的数字，则返回 true，否则返回 false。


```
use std::collections::HashMap;

/// 频率跟踪器
///
/// 维护每个数字的出现频率，以及每个频率对应的数字个数
/// 时间复杂度: 所有操作 O(1)
/// 空间复杂度: O(n)
struct FrequencyTracker {
    freq: HashMap<i32, i32>,      // 数字 -> 出现次数
    freq_count: HashMap<i32, i32>, // 频率 -> 拥有该频率的数字个数
}

impl FrequencyTracker {
    /// 初始化频率跟踪器
    fn new() -> Self {
        FrequencyTracker {
            freq: HashMap::new(),
            freq_count: HashMap::new(),
        }
    }

    /// 添加一个数字
    fn add(&mut self, number: i32) {
        let old_freq = self.freq.get(&number).copied().unwrap_or(0);
        let new_freq = old_freq + 1;

        // 更新旧频率计数（减少）
        if old_freq > 0 {
            *self.freq_count.entry(old_freq).or_insert(0) -= 1;
        }

        // 更新数字频率
        self.freq.insert(number, new_freq);

        // 更新新频率计数（增加）
        *self.freq_count.entry(new_freq).or_insert(0) += 1;
    }

    /// 删除一个数字（如果存在）
    fn delete_one(&mut self, number: i32) {
        let old_freq = self.freq.get(&number).copied().unwrap_or(0);
        if old_freq == 0 {
            return;
        }

        let new_freq = old_freq - 1;

        // 更新旧频率计数
        *self.freq_count.entry(old_freq).or_insert(0) -= 1;

        // 更新数字频率
        if new_freq == 0 {
            self.freq.remove(&number);
        } else {
            self.freq.insert(number, new_freq);
        }

        // 更新新频率计数
        if new_freq > 0 {
            *self.freq_count.entry(new_freq).or_insert(0) += 1;
        }
    }

    /// 检查是否存在出现 frequency 次的数字
    fn has_frequency(&self, frequency: i32) -> bool {
        self.freq_count.get(&frequency).copied().unwrap_or(0) > 0
    }
}
```
