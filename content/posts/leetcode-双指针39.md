---
title: "leetcode-双指针39"
date: 2026-09-05T09:53:03+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 回文子串


给你一个字符串 s ，请你统计并返回这个字符串中 回文子串 的数目。

回文字符串 是正着读和倒过来读一样的字符串。

子字符串 是字符串中的由连续字符组成的一个序列。

```
impl Solution {
    pub fn count_substrings(s: String) -> i32 {
        let s = s.as_bytes();
        let n = s.len();
        let mut count = 0;

        // 中心扩展法：每个中心向两边扩展
        for center in 0..(2 * n - 1) {
            let mut left = center / 2;
            let mut right = (center + 1) / 2; // 奇数中心 left==right，偶数中心 left+1==right

            while left < n && right < n && s[left] == s[right] {
                count += 1;
                if left == 0 { break; }
                left -= 1;
                right += 1;
            }
        }

        count
    }
}
```
