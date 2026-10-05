---
title: "leetcode-贪心154"
date: 2026-10-03T12:55:44+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 不相交子字符串的最大数量

给你一个字符串 word。

返回以 首尾字母相同 且 长度至少为 4 的 不相交子字符串 的最大数量。

子字符串 是字符串中连续的 非空 字符序列。


```
impl Solution {
    pub fn max_substrings(word: String) -> i32 {
        // 贪心：从左到右扫描，记录每个字母首次出现的位置。
        // 当再次遇到某字母且距离 >= 3（即子串长度 >= 4）时，
        // 立即切出一个有效子串，并重置所有记录，开始找下一个。
        let mut pos = [-1i32; 26];
        let mut ans = 0;

        for (i, b) in word.bytes().enumerate() {
            let c = (b - b'a') as usize;
            if pos[c] < 0 {
                pos[c] = i as i32;
            } else if i as i32 - pos[c] >= 3 {
                ans += 1;
                pos = [-1i32; 26]; // 重置，避免子串重叠
            }
        }
        ans
    }
}
```
