---
title: "leetcode-计数45"
date: 2026-08-01T10:40:53+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 重新分配字符使所有字符串都相等

给你一个字符串数组 words（下标 从 0 开始 计数）。

在一步操作中，需先选出两个 不同 下标 i 和 j，其中 words[i] 是一个非空字符串，接着将 words[i] 中的 任一 字符移动到 words[j] 中的 任一 位置上。

如果执行任意步操作可以使 words 中的每个字符串都相等，返回 true ；否则，返回 false 。


```
impl Solution {
    pub fn make_equal(words: Vec<String>) -> bool {
        let n = words.len();
        let mut count = [0; 26];

        // 统计所有字符出现的总次数
        for word in words {
            for &b in word.as_bytes() {
                count[(b - b'a') as usize] += 1;
            }
        }

        // 每个字符的总次数必须能被 n 整除
        count.iter().all(|&c| c % n == 0)
    }
}
```
