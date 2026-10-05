---
title: "leetcode-双指针63"
date: 2026-09-05T09:53:04+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 按字典序排在最后的子串

给你一个字符串 s ，找出它的所有子串并按字典序排列，返回排在最后的那个子串。


```
impl Solution {
    pub fn last_substring(s: String) -> String {
        let bytes = s.as_bytes();
        let n = bytes.len();

        let (mut i, mut j, mut k) = (0, 1, 0);

        while j + k < n {
            match bytes[i + k].cmp(&bytes[j + k]) {
                std::cmp::Ordering::Equal => k += 1,
                std::cmp::Ordering::Less => {
                    // i..=i+k 区间的任何起始位置都不可能成为最大子串
                    // 因为对应 j 位置的子串更大（前 k 个字符相同，第 k+1 个字符更大）
                    i = i + k + 1;
                    k = 0;
                    if i >= j {
                        j = i + 1;
                    }
                }
                std::cmp::Ordering::Greater => {
                    j = j + k + 1;
                    k = 0;
                }
            }
        }

        s[i..].to_string()
    }
}
```
