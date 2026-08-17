---
title: "leetcode-计数97"
date: 2026-08-01T10:40:56+08:00
tags: ["leetcode", "计数"]
draft: false
---


## K 周期字符串需要的最少操作次数

给你一个长度为 n 的字符串 word 和一个整数 k ，其中 k 是 n 的因数。

在一次操作中，你可以选择任意两个下标 i 和 j，其中 0 <= i, j < n ，且这两个下标都可以被 k 整除，然后用从 j 开始的长度为 k 的子串替换从 i 开始的长度为 k 的子串。也就是说，将子串 word[i..i + k - 1] 替换为子串 word[j..j + k - 1] 。

返回使 word 成为 K 周期字符串 所需的 最少 操作次数。

如果存在某个长度为 k 的字符串 s，使得 word 可以表示为任意次数连接 s ，则称字符串 word 是 K 周期字符串 。例如，如果 word == "ababab"，那么 word 就是 s = "ab" 时的 2 周期字符串 。


```
impl Solution {
    pub fn minimum_operations_to_make_k_periodic(word: String, k: i32) -> i32 {
        let k = k as usize;
        let n = word.len();
        let parts = n / k;

        // 收集所有长度为 k 的子串（零拷贝引用）
        let mut chunks: Vec<&str> = (0..n)
            .step_by(k)
            .map(|i| &word[i..i + k])
            .collect();

        // 排序后找出现次数最多的子串
        chunks.sort_unstable();

        let mut max_freq = 1;
        let mut cur_freq = 1;
        for i in 1..chunks.len() {
            if chunks[i] == chunks[i - 1] {
                cur_freq += 1;
                max_freq = max_freq.max(cur_freq);
            } else {
                cur_freq = 1;
            }
        }

        // 需要把其他段改成出现最多的那段
        parts as i32 - max_freq as i32
    }
}
```
