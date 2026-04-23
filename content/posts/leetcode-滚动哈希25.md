---
title: "leetcode-滚动哈希25"
date: 2026-04-17T20:00:32+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 恢复空格

哦，不！你不小心把一个长篇文章中的空格、标点都删掉了，并且大写也弄成了小写。像句子"I reset the computer. It still didn’t boot!"已经变成了"iresetthecomputeritstilldidntboot"。在处理标点符号和大小写之前，你得先把它断成词语。当然了，你有一本厚厚的词典dictionary，不过，有些词没在词典里。假设文章用sentence表示，设计一个算法，把文章断开，要求未识别的字符最少，返回未识别的字符数。

注意：本题相对原题稍作改动，只需返回未识别的字符数

```
impl Solution {
    /// 恢复空格：计算句子中最少未识别的字符数
    ///
    /// # 算法思路
    /// - 将字典单词**倒序**插入字典树，便于从句子末尾向前匹配单词后缀。
    /// - 动态规划：`dp[i]` 表示句子前 `i` 个字符的最少未识别字符数。
    /// - 对每个位置 `i`，尝试以 `i` 结尾的不同长度子串在字典树中匹配，
    ///   若匹配到完整单词，则可从 `dp[i - word_len]` 转移而来。
    pub fn respace(dictionary: Vec<String>, sentence: String) -> i32 {
        // 构建字典树（函数式风格：用 fold 累积插入操作）
        let trie = dictionary
            .iter()
            .fold(Trie::new(), |mut root, word| {
                root.insert_reversed(word);
                root
            });

        let bytes = sentence.as_bytes();
        let n = bytes.len();

        // dp[i] 表示前 i 个字符的最少未识别数，初始化为一个极大值，dp[0] = 0
        let mut dp = vec![i32::MAX; n + 1];
        dp[0] = 0;

        // 遍历每个前缀长度 i
        for i in 1..=n {
            // 情况1：当前字符未被任何单词覆盖，未识别数 +1
            dp[i] = dp[i - 1].saturating_add(1);

            // 情况2：尝试在字典树中匹配以 i 结尾的子串（倒序匹配）
            let mut node = &trie;
            // 从 i-1 位置向前扫描，step 表示已匹配的字符数
            for (step, &byte) in bytes[..i].iter().rev().enumerate() {
                let idx = (byte - b'a') as usize;
                if let Some(child) = &node.children[idx] {
                    node = child;
                    // 若当前节点是一个单词的结尾，则找到了一个有效单词
                    if node.is_end_of_word {
                        let word_len = step + 1;          // 单词长度
                        let prev_idx = i - word_len;       // 单词起始前的位置
                        dp[i] = dp[i].min(dp[prev_idx]);   // 尝试从 dp[prev_idx] 转移
                    }
                } else {
                    // 当前字符不匹配，无法继续匹配更长的后缀，提前退出
                    break;
                }
            }
        }

        dp[n]
    }
}

/// 字典树节点（仅支持小写字母 a-z）
struct Trie {
    children: [Option<Box<Trie>>; 26],
    is_end_of_word: bool,
}

impl Trie {
    /// 创建一个空节点
    fn new() -> Self {
        Self {
            children: Default::default(), // 26 个 None
            is_end_of_word: false,
        }
    }

    /// 将一个单词**倒序**插入字典树
    ///
    /// 例如 "looked" 会被插入为 'd' -> 'e' -> 'k' -> 'o' -> 'o' -> 'l'
    /// 这样在匹配句子时，可以从后向前扫描字符，在树中正向行走。
    fn insert_reversed(&mut self, word: &str) {
        // 使用 fold 从根节点开始，逐字符向下创建/获取子节点
        word.chars()
            .rev()                               // 反转字符顺序
            .fold(self, |node, ch| {
                let idx = (ch as u8 - b'a') as usize;
                // 若子节点不存在则创建，并返回其可变引用
                node.children[idx].get_or_insert_with(|| Box::new(Trie::new()))
            })
            .is_end_of_word = true;              // 最后一个节点标记为单词结尾
    }
}
```
