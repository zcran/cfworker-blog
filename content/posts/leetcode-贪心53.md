---
title: "leetcode-贪心53"
date: 2026-10-03T12:55:37+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 检查一个字符串是否可以打破另一个字符串

给你两个字符串 s1 和 s2 ，它们长度相等，请你检查是否存在一个 s1  的排列可以打破 s2 的一个排列，或者是否存在一个 s2 的排列可以打破 s1 的一个排列。

字符串 x 可以打破字符串 y （两者长度都为 n ）需满足对于所有 i（在 0 到 n - 1 之间）都有 x[i] >= y[i]（字典序意义下的顺序）。


```
impl Solution {
    pub fn check_if_can_break(s1: String, s2: String) -> bool {
        // 统计每个字符出现次数，再转为后缀和：
        // freq[c] = 字符 >= c 的字符总数
        fn suffix_freq(s: &str) -> [i32; 26] {
            let mut freq = [0i32; 26];
            for b in s.bytes() {
                freq[(b - b'a') as usize] += 1;
            }
            for i in (0..25).rev() {
                freq[i] += freq[i + 1];
            }
            freq
        }

        let (f1, f2) = (suffix_freq(&s1), suffix_freq(&s2));
        // 对每个字符 c，比较 s1 中 >= c 的数量与 s2 中 >= c 的数量。
        // 若存在 c 使 s1 的更多、另一处使 s2 的更多，则两种打破方向都不成立。
        let mut sign = 0; // 0 未定，1 表示 s1 可打破 s2，-1 表示反之
        for i in 0..26 {
            match f1[i].cmp(&f2[i]) {
                std::cmp::Ordering::Greater => {
                    if sign == -1 {
                        return false;
                    }
                    sign = 1;
                }
                std::cmp::Ordering::Less => {
                    if sign == 1 {
                        return false;
                    }
                    sign = -1;
                }
                std::cmp::Ordering::Equal => {}
            }
        }
        true
    }
}
```
