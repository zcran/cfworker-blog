---
title: "leetcode-回溯53"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 重复 K 次的最长子序列

给你一个长度为 n 的字符串 s ，和一个整数 k 。请你找出字符串 s 中 重复 k 次的 最长子序列 。

子序列 是由其他字符串删除某些（或不删除）字符派生而来的一个字符串。

如果 seq * k 是 s 的一个子序列，其中 seq * k 表示一个由 seq 串联 k 次构造的字符串，那么就称 seq 是字符串 s 中一个 重复 k 次 的子序列。

举个例子，"bba" 是字符串 "bababcba" 中的一个重复 2 次的子序列，因为字符串 "bbabba" 是由 "bba" 串联 2 次构造的，而 "bbabba" 是字符串 "bababcba" 的一个子序列。

返回字符串 s 中 重复 k 次的最长子序列  。如果存在多个满足的子序列，则返回 字典序最大 的那个。如果不存在这样的子序列，返回一个 空 字符串。


```
use std::collections::VecDeque;

impl Solution {
    pub fn longest_subsequence_repeated_k(s: String, k: i32) -> String {
        let s_chars: Vec<char> = s.chars().collect();
        let k = k as usize;

        // 统计每个字符出现的总次数
        let mut char_freq = [0; 26];
        for &ch in &s_chars {
            char_freq[(ch as u8 - b'a') as usize] += 1;
        }

        // 检查候选子序列重复 k 次是否为 s 的子序列
        let is_valid = |sub: &[char]| -> bool {
            let mut sub_idx = 0;
            let mut remaining = k;

            for &ch in &s_chars {
                if ch == sub[sub_idx] {
                    sub_idx += 1;
                    // 完成一次完整匹配
                    if sub_idx == sub.len() {
                        remaining -= 1;
                        if remaining == 0 {
                            return true;
                        }
                        sub_idx = 0;
                    }
                }
            }
            false
        };

        let mut answer = String::new();
        let mut queue = VecDeque::new();
        queue.push_back(Vec::new()); // BFS 起点：空序列

        // BFS 逐层扩展，先保证长度优先
        while let Some(sub) = queue.pop_front() {
            // 按字母顺序从小到大尝试，保证同一层中后更新的字典序更大
            for i in 0..26 {
                // 该字符出现次数不足以构成 k 次重复
                if char_freq[i] < k as i32 {
                    continue;
                }

                let ch = (i as u8 + b'a') as char;
                let mut candidate = sub.clone();
                candidate.push(ch);

                // 如果候选有效，更新答案并继续扩展
                if is_valid(&candidate) {
                    answer = candidate.iter().collect();
                    queue.push_back(candidate);
                }
            }
        }

        answer
    }
}
```
