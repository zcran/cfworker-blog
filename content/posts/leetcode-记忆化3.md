---
title: "leetcode-记忆化3"
date: 2026-05-06T20:22:33+08:00
tags: ["leetcode", "记忆化"]
draft: false
---

## 单词拆分 II

给定一个字符串 s 和一个字符串字典 wordDict ，在字符串 s 中增加空格来构建一个句子，使得句子中所有的单词都在词典中。以任意顺序 返回所有这些可能的句子。

注意：词典中的同一个单词可能在分段中被重复使用多次。

```
use std::collections::{HashSet, HashMap};

impl Solution {
    /// 返回字符串 `s` 所有可能的由 `word_dict` 中的单词组成的句子。
    ///
    /// 采用带记忆化的回溯法（函数式风格），避免重复计算子问题。
    pub fn word_break(s: String, word_dict: Vec<String>) -> Vec<String> {
        // 将单词字典转换为 HashSet<&str>，所有 &str 都借用了 word_dict 中的字符串
        // 生命周期与函数调用等价
        let dict: HashSet<&str> = word_dict.iter().map(|word| word.as_str()).collect();
        // 缓存：每个子串对应的所有可能句子，键的生命周期为 'a
        let mut cache: HashMap<&str, Vec<String>> = HashMap::new();

        /// 递归函数，返回当前子串 `s` 的所有分割结果
        ///
        /// 生命周期 `'a` 表示所有传入的字符串切片及字典中的单词都来自同一生命周期，
        /// 且缓存中的键也使用该生命周期。
        fn dfs<'a>(
            s: &'a str,
            dict: &HashSet<&'a str>,
            cache: &mut HashMap<&'a str, Vec<String>>,
        ) -> Vec<String> {
            // 空串：认为存在一种分割（空句子）
            if s.is_empty() {
                return vec![String::new()];
            }
            // 如果已经计算过，直接返回缓存结果
            if let Some(res) = cache.get(s) {
                return res.clone();
            }

            let mut results = Vec::new();
            // 遍历所有可能的前缀长度
            for i in 0..s.len() {
                let prefix = &s[..=i];
                if dict.contains(prefix) {
                    // 递归处理后缀部分
                    let suffix_results = dfs(&s[i + 1..], dict, cache);
                    // 将当前前缀与每个后缀句子组合成完整句子
                    for suffix in suffix_results {
                        if suffix.is_empty() {
                            results.push(prefix.to_string());
                        } else {
                            results.push(format!("{} {}", prefix, suffix));
                        }
                    }
                }
            }
            // 存入缓存并返回
            cache.insert(s, results.clone());
            results
        }

        dfs(&s, &dict, &mut cache)
    }
}
```
