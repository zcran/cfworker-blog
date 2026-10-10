---
title: "leetcode-排序1"
date: 2026-10-05T00:34:59+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 字母异位词分组

给你一个字符串数组，请你将 字母异位词 组合在一起。可以按任意顺序返回结果列表。


```
use std::collections::HashMap;

impl Solution {
    /// 将字母异位词分组
    ///
    /// 以「排序后的字节序列」作为哈希键：互为字母异位词的字符串排序后必然相同，
    /// 因此会被归入同一个桶中。
    pub fn group_anagrams(strs: Vec<String>) -> Vec<Vec<String>> {
        // key: 排序后的字节，value: 该组所有原始字符串
        let mut groups: HashMap<Vec<u8>, Vec<String>> = HashMap::new();

        for s in strs {
            // 用字节排序代替字符排序，避免 UTF-8 解码开销；
            // 本题限定为小写英文字母，字节序与字符序一致。
            let mut key = s.as_bytes().to_vec();
            key.sort_unstable();

            groups.entry(key).or_default().push(s);
        }

        // 只消费 value，避免额外的键遍历开销
        groups.into_values().collect()
    }
}
```
