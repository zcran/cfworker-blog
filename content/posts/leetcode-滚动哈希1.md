---
title: "leetcode-滚动哈希1"
date: 2026-04-17T20:00:31+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 重复的DNA序列

DNA序列 由一系列核苷酸组成，缩写为 'A', 'C', 'G' 和 'T'.。

例如，"ACGAATTCCG" 是一个 DNA序列 。
在研究 DNA 时，识别 DNA 中的重复序列非常有用。

给定一个表示 DNA序列 的字符串 s ，返回所有在 DNA 分子中出现不止一次的 长度为 10 的序列(子字符串)。你可以按 任意顺序 返回答案。


```
use std::collections::HashMap;

impl Solution {
    /// 找出所有重复出现一次以上的长度为 10 的 DNA 子串。
    ///
    /// 使用滚动哈希（Rabin–Karp）算法：
    /// - 将字符 A/C/G/T 映射为 2 位二进制数（0,1,2,3）
    /// - 每个子串编码为 20 位无符号整数，唯一标识该子串
    /// - 通过滑动窗口和位运算 O(1) 计算相邻哈希值
    /// - 用哈希表记录每个哈希值第一次出现的索引，重复时收集结果
    ///
    /// # 参数
    /// - `s`: 输入的 DNA 字符串
    ///
    /// # 返回值
    /// - 所有重复出现的长度为 10 的子串（无重复顺序）
    pub fn find_repeated_dna_sequences(s: String) -> Vec<String> {
        const WIN_LEN: usize = 10;                 // 窗口长度
        let bytes = s.as_bytes();
        let n = bytes.len();
        if n < WIN_LEN {
            return vec![];
        }

        // 字符 → 2 位编码
        let encode = |b: u8| -> u32 {
            match b {
                b'A' => 0,
                b'C' => 1,
                b'G' => 2,
                b'T' => 3,
                _ => unreachable!("Invalid DNA character"),
            }
        };

        // 预计算第一个窗口的哈希值
        let mut hash = 0u32;
        for i in 0..WIN_LEN {
            hash = (hash << 2) | encode(bytes[i]);
        }
        const MASK: u32 = (1 << (2 * WIN_LEN)) - 1; // 低 20 位掩码 (0xFFFFF)

        // seen: 哈希值 → 第一次出现的起始索引
        // repeated_set: 已记录到结果中的哈希值（避免重复添加相同子串）
        let mut seen = HashMap::new();
        let mut repeated_set = std::collections::HashSet::new();
        seen.insert(hash, 0);

        // 滑动窗口，计算后续哈希值
        for i in 1..=(n - WIN_LEN) {
            // 滚动哈希公式：移除最高两位，左移两位，加入新字符的低两位
            let new_char = encode(bytes[i + WIN_LEN - 1]);
            hash = ((hash << 2) | new_char) & MASK;

            if let Some(&first_idx) = seen.get(&hash) {
                // 该哈希值已出现过 → 子串重复
                // 仅当该子串尚未加入结果集时才记录（去重）
                if !repeated_set.contains(&hash) {
                    repeated_set.insert(hash);
                }
            } else {
                // 第一次出现，记录索引
                seen.insert(hash, i);
            }
        }

        // 将重复子串的哈希值还原为字符串
        // 利用 seen 中存储的第一次出现索引提取子串
        repeated_set
            .into_iter()
            .map(|h| {
                let idx = seen[&h];               // 安全：重复哈希必在 seen 中
                s[idx..idx + WIN_LEN].to_string()
            })
            .collect()
    }
}
```
