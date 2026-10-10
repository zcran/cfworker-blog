---
title: "leetcode-排序89"
date: 2026-10-05T00:35:05+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 判定是否互为字符重排

给定两个由小写字母组成的字符串 s1 和 s2，请编写一个程序，确定其中一个字符串的字符重新排列后，能否变成另一个字符串。


```
impl Solution {
    pub fn check_permutation(s1: String, s2: String) -> bool {
        if s1.len() != s2.len() {
            return false;
        }

        // 统计 s1 中每个字符的净计数：s1 加 1，s2 减 1
        let mut counts = [0i32; 26];
        for (a, b) in s1.bytes().zip(s2.bytes()) {
            counts[(a - b'a') as usize] += 1;
            counts[(b - b'a') as usize] -= 1;
        }

        // 所有计数归零，说明两字符串字符频次完全相同
        counts.iter().all(|&c| c == 0)
    }
}
```
