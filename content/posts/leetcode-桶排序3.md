---
title: "Leetcode 桶排序3"
date: 2023-12-19T15:46:22+08:00
tags: ["leetcode", "桶排序"]
draft: false
---

## 前K个高频单词

给定一个单词列表 words 和一个整数 k ，返回前 k 个出现次数最多的单词。

返回的答案应该按单词出现频率由高到低排序。如果不同的单词有相同出现频率， 按字典顺序 排序。

```
use std::collections::{HashMap, BTreeSet};
impl Solution {
    pub fn top_k_frequent(words: Vec<String>, k: i32) -> Vec<String> {
        let mut cnt = HashMap::new();
        let mut max_freq = 0;
        for word in words {
            let entry = cnt.entry(word).or_insert(0);
            *entry += 1;
            max_freq = max_freq.max(*entry);
        }
        let mut buckets = vec![BTreeSet::new(); max_freq as usize + 1];
        for (word, freq) in cnt.iter() {
            buckets[*freq as usize].insert(word.clone());
        }
        let mut ans = Vec::new();
        let mut k = k;
        for i in (0..=max_freq as usize).rev() {
            for word in buckets[i].iter() {
                ans.push(word.clone());
                k -= 1;
                if k == 0 {
                    break;
                }
            }
            if k == 0 {
                break;
            }
        }
        ans
    }
}
```