---
title: "leetcode-计数96"
date: 2026-08-01T10:40:56+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 成为 K 特殊字符串需要删除的最少字符数

给你一个字符串 word 和一个整数 k。

如果 |freq(word[i]) - freq(word[j])| <= k 对于字符串中所有下标 i 和 j  都成立，则认为 word 是 k 特殊字符串。

此处，freq(x) 表示字符 x 在 word 中的出现频率，而 |y| 表示 y 的绝对值。

返回使 word 成为 k 特殊字符串 需要删除的字符的最小数量。


```
impl Solution {
    pub fn minimum_deletions(word: String, k: i32) -> i32 {
        // 统计各字符频率（只有小写字母，用数组替代 HashMap 更高效）
        let mut freq = [0; 26];
        for b in word.bytes() {
            freq[(b - b'a') as usize] += 1;
        }

        // 收集所有非零频率，减少后续枚举量
        let freqs: Vec<i32> = freq.into_iter().filter(|&f| f > 0).collect();
        let mut res = word.len() as i32;

        // 枚举每个频率作为目标基准值 a
        // 最终所有频率需落在 [a, a+k] 区间内
        for &a in &freqs {
            let deleted: i32 = freqs.iter()
                .map(|&b| {
                    if b < a { b }                  // 频率太低：全部删除
                    else if b > a + k { b - a - k } // 频率太高：删到 a+k
                    else { 0 }                      // 在范围内：不删
                })
                .sum();
            res = res.min(deleted);
        }

        res
    }
}
```
