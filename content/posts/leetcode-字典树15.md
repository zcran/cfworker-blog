---
title: "leetcode-字典树15"
date: 2026-06-02T10:23:07+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 匹配子序列的单词数


给定字符串 s 和字符串数组 words, 返回  words[i] 中是s的子序列的单词个数 。

字符串的 子序列 是从原始字符串中生成的新字符串，可以从中删去一些字符(可以是none)，而不改变其余字符的相对顺序。

例如， “ace” 是 “abcde” 的子序列。

```
/// 配对结构体：存储单词及其当前匹配进度
///
/// 用于在队列中跟踪每个单词已匹配到哪个位置
#[derive(Clone)]  // 派生 Clone trait，允许复制 Pair 实例
struct Pair {
    curr: String,  // 当前单词
    idx: i32,      // 已匹配的字符数（下一个要匹配的字符索引）
}

impl Solution {
    /// 统计 words 中有多少个单词是字符串 s 的子序列
    ///
    /// # 参数
    /// - `s`: 主字符串
    /// - `words`: 待检查的单词列表
    ///
    /// # 返回
    /// 作为 s 子序列的单词数量
    ///
    /// # 示例
    /// ```
    /// let s = "abcde".to_string();
    /// let words = vec!["a".to_string(), "bb".to_string(), "acd".to_string(), "ace".to_string()];
    /// assert_eq!(Solution::num_matching_subseq(s, words), 3);
    /// // "a", "acd", "ace" 是子序列，"bb" 不是
    /// ```
    ///
    /// # 算法思想
    /// 使用桶队列（Bucket Queue）优化：
    /// 1. 按单词的当前需要匹配的字符分桶
    /// 2. 遍历 s 的每个字符，只处理匹配该字符的单词
    /// 3. 单词匹配后移到下一个字符的桶中
    /// 4. 完全匹配的单词计数
    pub fn num_matching_subseq(s: String, words: Vec<String>) -> i32 {
        // 导入双端队列（VecDeque）用于高效的头尾操作
        use std::collections::VecDeque;

        // 创建 26 个桶，每个桶对应一个字母（a-z）
        // 每个桶是一个双端队列，存储等待匹配该字母的单词
        let mut queue = vec![VecDeque::new(); 26];

        // 计数器：记录匹配成功的单词数量
        let mut cnt = 0;

        // 步骤 1: 初始化所有单词
        // 将每个单词放入其首字母对应的桶中
        for word in words {
            // 获取单词的第一个字符
            let first_char = word.as_bytes()[0];
            // 计算桶索引（'a' -> 0, 'b' -> 1, ...）
            let bucket_idx = (first_char - b'a') as usize;
            // 创建 Pair 对象并加入对应桶的队列
            queue[bucket_idx].push_back(Pair {
                curr: word,  // 单词本身
                idx: 0,      // 初始匹配进度为 0
            });
        }

        // 步骤 2: 遍历主字符串 s 的每个字符
        for ch in s.as_bytes() {
            // 计算当前字符对应的桶索引
            let ch_idx = (*ch - b'a') as usize;

            // 获取该桶中待处理单词的数量
            // 注意：必须先获取长度，因为稍后会修改队列
            let bucket_size = queue[ch_idx].len();

            // 处理当前桶中的所有单词
            for _ in 0..bucket_size {
                // 从队列头部取出一个待匹配的单词
                let mut pair = queue[ch_idx].pop_front().unwrap();

                // 匹配当前字符，进度 +1
                pair.idx += 1;

                // 检查单词是否已经完全匹配
                if pair.curr.len() == pair.idx as usize {
                    // 单词完全匹配，计数加 1
                    cnt += 1;
                    // 继续处理下一个单词（不重新入队）
                    continue;
                }

                // 单词尚未完全匹配，计算下一个需要匹配的字符
                let next_char = pair.curr.as_bytes()[pair.idx as usize];
                let next_bucket_idx = (next_char - b'a') as usize;

                // 将单词放入下一个字符对应的桶中
                queue[next_bucket_idx].push_back(pair);
            }
        }

        // 返回匹配成功的单词数量
        cnt
    }
}
```
