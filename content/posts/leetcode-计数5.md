---
title: "leetcode-计数5"
date: 2026-08-01T10:40:51+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 根据字符出现频率排序

给定一个字符串 s ，根据字符出现的 频率 对其进行 降序排序 。一个字符出现的 频率 是它出现在字符串中的次数。

返回 已排序的字符串 。如果有多个答案，返回其中任何一个。


```
use std::collections::HashMap;

impl Solution {
    /// 根据字符出现频率对字符串进行降序排序
    ///
    /// # 算法思路
    /// 1. 统计每个字符的出现次数（哈希表）
    /// 2. 将字符按频率分组（桶排序）
    /// 3. 从高频到低频遍历桶，构建结果字符串
    ///
    /// # 复杂度
    /// - 时间：O(n)，n 为字符串长度
    /// - 空间：O(n)，用于存储桶和结果
    pub fn frequency_sort(s: String) -> String {
        let n = s.len();

        // 1. 统计字符频率
        let mut freq_map = HashMap::with_capacity(52); // 大小写字母最多 52 种
        for ch in s.chars() {
            *freq_map.entry(ch).or_insert(0) += 1;
        }

        // 2. 桶排序：索引 = 频率，值 = 该频率对应的字符列表
        // 最大频率不会超过字符串长度
        let mut buckets = vec![Vec::new(); n + 1];
        for (ch, count) in freq_map {
            buckets[count].push(ch);
        }

        // 3. 从高频到低频构建结果
        let mut result = String::with_capacity(n);
        for count in (1..=n).rev() {
            for &ch in &buckets[count] {
                // 重复字符 count 次
                result.extend(std::iter::repeat(ch).take(count));
            }
        }

        result
    }
}
```
