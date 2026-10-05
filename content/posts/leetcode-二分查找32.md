---
title: "leetcode-二分查找32"
date: 2026-09-28T12:29:12+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 比较字符串最小字母出现频次

定义一个函数 f(s)，统计 s  中（按字典序比较）最小字母的出现频次 ，其中 s 是一个非空字符串。

例如，若 s = "dcce"，那么 f(s) = 2，因为字典序最小字母是 "c"，它出现了 2 次。

现在，给你两个字符串数组待查表 queries 和词汇表 words 。对于每次查询 queries[i] ，需统计 words 中满足 f(queries[i]) < f(W) 的 词的数目 ，W 表示词汇表 words 中的每个词。

请你返回一个整数数组 answer 作为答案，其中每个 answer[i] 是第 i 次查询的结果。


```
impl Solution {
    /// 返回每次查询的结果：words 中 f(W) > f(queries[i]) 的词数。
    ///
    /// 其中 f(s) 为字符串 s 中字典序最小字母的出现次数。
    ///
    /// 思路：
    ///   1. 先计算 words 中每个词的 f 值，排序。
    ///   2. 对每个 query 计算其 f 值 q，用二分找出排序数组中
    ///      第一个大于 q 的位置 p，则 len - p 即为满足条件的词数。
    pub fn num_smaller_by_frequency(queries: Vec<String>, words: Vec<String>) -> Vec<i32> {
        /// 计算字符串中字典序最小字母的出现次数
        fn f(s: &str) -> usize {
            let mut min_c = 'z';
            let mut cnt = 0;
            for c in s.chars() {
                if c < min_c {
                    min_c = c;
                    cnt = 1;
                } else if c == min_c {
                    cnt += 1;
                }
            }
            cnt
        }

        // 计算 words 中每个词的 f 值并排序
        let mut freqs: Vec<usize> = words.iter().map(|w| f(w)).collect();
        freqs.sort_unstable();
        let len = freqs.len();

        // 对每个查询，二分找第一个 > q 的位置
        queries
            .iter()
            .map(|q| {
                let q = f(q);
                let pos = freqs.partition_point(|&x| x <= q);
                (len - pos) as i32
            })
            .collect()
    }
}
```
