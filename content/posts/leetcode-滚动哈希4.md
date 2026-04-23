---
title: "leetcode-滚动哈希4"
date: 2026-04-17T20:00:31+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 最长重复子串

给你一个字符串 s ，考虑其所有 重复子串 ：即 s 的（连续）子串，在 s 中出现 2 次或更多次。这些出现之间可能存在重叠。

返回 任意一个 可能具有最长长度的重复子串。如果 s 不含重复子串，那么答案为 "" 。


```
use std::collections::HashMap;

impl Solution {
    /// 返回给定字符串中最长的重复子串（连续出现至少两次的子串）。
    /// 使用**滚动哈希（Rabin–Karp）** + **二分查找**。
    ///
    /// # 原理
    /// - 单调性：若存在长度为 L 的重复子串，则必存在长度 < L 的重复子串 → 二分答案。
    /// - 检查函数 O(n) 判断是否存在长度为 len 的重复子串。
    /// - 滚动哈希将每个子串压缩为 u64 整数（模 2^64 自然溢出），配合哈希表记录首次出现位置。
    /// - 哈希冲突时通过直接比较子串内容二次验证，确保正确性。
    ///
    /// # 参数
    /// - `s`: 输入字符串
    ///
    /// # 返回值
    /// - 最长的重复子串，若不存在则返回空字符串
    pub fn longest_dup_substring(s: String) -> String {
        let n = s.len();
        if n < 2 {
            return String::new();
        }

        // 二分搜索最大可行长度
        let mut lo = 0;
        let mut hi = n - 1;
        while lo < hi {
            let mid = (lo + hi + 1) / 2; // 上取整，避免死循环
            if Self::has_duplicate_of_len(&s, mid) {
                lo = mid;
            } else {
                hi = mid - 1;
            }
        }

        // 获取该长度下的任意一个重复子串
        Self::find_duplicate_of_len(&s, lo).unwrap_or_default()
    }

    /// 检查是否存在长度为 `len` 的重复子串
    fn has_duplicate_of_len(s: &str, len: usize) -> bool {
        if len == 0 {
            return true;
        }
        Self::find_duplicate_of_len(s, len).is_some()
    }

    /// 查找并返回第一个长度为 `len` 的重复子串（若存在）
    /// 使用滚动哈希 + 哈希表 + 二次验证。
    fn find_duplicate_of_len(s: &str, len: usize) -> Option<String> {
        let bytes = s.as_bytes();
        let n = bytes.len();
        if len == 0 || len > n {
            return None;
        }

        const BASE: u64 = 29;                 // 基数（大于字符集大小 'a'..='z'）
        let mut pow_len_minus_1 = 1u64;       // BASE^(len-1)，用于滚动移除最左字符

        // 预计算 BASE^(len-1)
        for _ in 0..len - 1 {
            pow_len_minus_1 = pow_len_minus_1.wrapping_mul(BASE);
        }

        // 计算第一个窗口的哈希值（长度为 len）
        let mut hash = 0u64;
        for i in 0..len {
            let val = (bytes[i] - b'a') as u64;
            hash = hash.wrapping_mul(BASE).wrapping_add(val);
        }

        // 哈希表：哈希值 → 首次出现的起始索引
        let mut seen = HashMap::new();
        seen.insert(hash, 0);

        // 滑动窗口，计算后续窗口的哈希值
        for start in 1..=(n - len) {
            let left_val = (bytes[start - 1] - b'a') as u64;
            let right_val = (bytes[start + len - 1] - b'a') as u64;

            // 正确滚动公式：
            // new_hash = (old_hash - left_val * BASE^(len-1)) * BASE + right_val
            hash = hash
                .wrapping_sub(left_val.wrapping_mul(pow_len_minus_1)) // 移除左字符
                .wrapping_mul(BASE)                                   // 左移
                .wrapping_add(right_val);                             // 加入右字符

            // 检查该哈希值是否出现过
            if let Some(&prev_start) = seen.get(&hash) {
                // 二次验证：直接比较子串内容，避免哈希碰撞
                if &bytes[prev_start..prev_start + len] == &bytes[start..start + len] {
                    return Some(s[start..start + len].to_string());
                }
            } else {
                seen.insert(hash, start);
            }
        }

        None
    }
}
```
