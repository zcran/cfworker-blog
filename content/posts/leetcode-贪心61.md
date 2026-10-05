---
title: "leetcode-贪心61"
date: 2026-10-03T12:55:38+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 字符频次唯一的最小删除次数

如果字符串 s 中 不存在 两个不同字符 频次 相同的情况，就称 s 是 优质字符串 。

给你一个字符串 s，返回使 s 成为 优质字符串 需要删除的 最小 字符数。

字符串中字符的 频次 是该字符在字符串中的出现次数。例如，在字符串 "aab" 中，'a' 的频次是 2，而 'b' 的频次是 1 。


```
impl Solution {
    pub fn min_deletions(s: String) -> i32 {
        // 统计每个字母的出现次数
        let mut freq = [0i32; 26];
        for b in s.bytes() {
            freq[(b - b'a') as usize] += 1;
        }

        // 从小到大排序，从右往左保证每个频次严格递减
        freq.sort_unstable();

        let mut deletions = 0;
        for i in (1..26).rev() {
            if freq[i] <= freq[i - 1] {
                // 需要把 freq[i-1] 降到 freq[i] - 1；若降到负数则直接清零
                let target = (freq[i] - 1).max(0);
                deletions += freq[i - 1] - target;
                freq[i - 1] = target;
            }
        }

        deletions
    }
}
```
