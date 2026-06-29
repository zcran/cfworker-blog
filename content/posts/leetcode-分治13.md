---
title: "leetcode-分治13"
date: 2026-06-13T10:51:38+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 至少有 K 个重复字符的最长子串

给你一个字符串 s 和一个整数 k ，请你找出 s 中的最长子串， 要求该子串中的每一字符出现次数都不少于 k 。返回这一子串的长度。

如果不存在这样的子字符串，则返回 0。

```
impl Solution {
    /// 枚举子串中出现的字符种类数，使用滑动窗口
    /// 时间复杂度 O(26 * n) ≈ O(n)，空间复杂度 O(26)
    pub fn longest_substring(s: String, k: i32) -> i32 {
        let bytes = s.as_bytes();
        let k = k as usize;
        let n = bytes.len();
        let mut max_len = 0;

        // 枚举子串中出现的不同字符种类数（1..=26）
        for unique_target in 1..=26 {
            let mut freq = [0; 26];
            let mut left = 0;
            let mut unique_count = 0;      // 当前窗口内不同字符数量
            let mut at_least_k_count = 0;   // 当前窗口内出现次数 >= k 的字符数量

            for right in 0..n {
                let idx = (bytes[right] - b'a') as usize;
                freq[idx] += 1;

                if freq[idx] == 1 {
                    unique_count += 1;
                }
                if freq[idx] == k {
                    at_least_k_count += 1;
                }

                // 缩小窗口直到不同字符数 <= unique_target
                while unique_count > unique_target {
                    let left_idx = (bytes[left] - b'a') as usize;
                    if freq[left_idx] == k {
                        at_least_k_count -= 1;
                    }
                    freq[left_idx] -= 1;
                    if freq[left_idx] == 0 {
                        unique_count -= 1;
                    }
                    left += 1;
                }

                // 如果窗口内所有不同字符都满足 >= k，更新答案
                if unique_count == unique_target && unique_count == at_least_k_count {
                    max_len = max_len.max(right - left + 1);
                }
            }
        }

        max_len as i32
    }
}
```
