---
title: "leetcode-位运算8"
date: 2026-09-24T10:33:34+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 最大单词长度乘积

给你一个字符串数组 words ，找出并返回 length(words[i]) * length(words[j]) 的最大值，并且这两个单词不含有公共字母。如果不存在这样的两个单词，返回 0 。


```
impl Solution {
    /// 核心思路：
    /// 用一个 26 位的整数 mask 表示每个单词包含哪些字母（第 k 位为 1 表示含第 k 个字母）。
    /// 两个单词不含公共字母 等价于 mask[i] & mask[j] == 0。
    ///
    /// 预处理出所有 mask 和长度后，两两枚举判断即可。
    /// 单词数 n 较小（≤ 1000），O(n²) 可以接受。
    pub fn max_product(words: Vec<String>) -> i32 {
        // 预处理：每个单词的字母掩码 及其 长度
        let masks: Vec<(i32, usize)> = words
            .iter()
            .map(|w| {
                let mask = w.bytes().fold(0i32, |acc, b| acc | 1 << (b - b'a'));
                (mask, w.len())
            })
            .collect();

        let mut ans = 0;
        for i in 0..masks.len() {
            for j in i + 1..masks.len() {
                // 无公共字母时更新答案
                if masks[i].0 & masks[j].0 == 0 {
                    ans = ans.max(masks[i].1 * masks[j].1);
                }
            }
        }

        ans as i32
    }
}
```
