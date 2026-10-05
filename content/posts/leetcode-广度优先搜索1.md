---
title: "leetcode-广度优先搜索1"
date: 2026-09-13T09:32:06+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 单词接龙

字典 wordList 中从单词 beginWord 到 endWord 的 转换序列 是一个按下述规格形成的序列 beginWord -> s1 -> s2 -> ... -> sk：

· 每一对相邻的单词只差一个字母。
· 对于 1 <= i <= k 时，每个 si 都在 wordList 中。注意， beginWord 不需要在 wordList 中。
· sk == endWord

给你两个单词 beginWord 和 endWord 和一个字典 wordList ，返回 从 beginWord 到 endWord 的 最短转换序列 中的 单词数目 。如果不存在这样的转换序列，返回 0 。


```
use std::collections::HashSet;

impl Solution {
    pub fn ladder_length(begin_word: String, end_word: String, word_list: Vec<String>) -> i32 {
        // 将字典转为 HashSet，便于 O(1) 查找
        let mut dict: HashSet<String> = word_list.into_iter().collect();

        // 终点不在字典中，直接无解
        if !dict.contains(&end_word) {
            return 0;
        }

        // 双向 BFS：分别从起点和终点扩散
        let mut begin_set: HashSet<String> = HashSet::new();
        let mut end_set: HashSet<String> = HashSet::new();
        begin_set.insert(begin_word.clone());
        end_set.insert(end_word.clone());

        // 从字典中移除起点和终点，避免重复访问
        dict.remove(&begin_word);
        dict.remove(&end_word);

        let mut step = 1;

        while !begin_set.is_empty() && !end_set.is_empty() {
            // 每次扩散较小的集合，减少搜索扇面
            if begin_set.len() > end_set.len() {
                std::mem::swap(&mut begin_set, &mut end_set);
            }

            let mut next_level: HashSet<String> = HashSet::new();

            for word in &begin_set {
                let mut bytes = word.clone().into_bytes();

                for i in 0..bytes.len() {
                    let old = bytes[i];

                    // 尝试将第 i 位替换为其他 25 个字母
                    for c in b'a'..=b'z' {
                        if c == old {
                            continue;
                        }
                        bytes[i] = c;

                        // 安全转换：替换后仍是合法 UTF-8（仅 ASCII 字母）
                        let target = unsafe { std::str::from_utf8_unchecked(&bytes) };

                        // 与另一侧相遇，返回总步数
                        if end_set.contains(target) {
                            return step + 1;
                        }

                        // 在字典中则加入下一层，并立即移除避免重复访问
                        if dict.remove(target) {
                            next_level.insert(target.to_string());
                        }
                    }

                    bytes[i] = old;
                }
            }

            begin_set = next_level;
            step += 1;
        }

        0
    }
}
```
