---
title: "leetcode-滑动窗口8"
date: 2026-07-18T11:02:29+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 找到字符串中所有字母异位词

给定两个字符串 s 和 p，找到 s 中所有 p 的 异位词 的子串，返回这些子串的起始索引。不考虑答案输出的顺序。


```
impl Solution {
    pub fn find_anagrams(s: String, p: String) -> Vec<i32> {
        let s = s.as_bytes();
        let p = p.as_bytes();
        let (n, m) = (s.len(), p.len());
        if n < m {
            return vec![];
        }

        // 统计 p 中字符频率，并记录差异数
        let mut diff = [0; 26];
        for &ch in p {
            diff[(ch - b'a') as usize] += 1;
        }

        let mut result = vec![];
        // 初始化第一个窗口
        for i in 0..m {
            diff[(s[i] - b'a') as usize] -= 1;
        }

        // 检查第一个窗口
        if diff.iter().all(|&x| x == 0) {
            result.push(0);
        }

        // 滑动窗口
        for i in m..n {
            // 移出左边界字符
            diff[(s[i - m] - b'a') as usize] += 1;
            // 移入右边界字符
            diff[(s[i] - b'a') as usize] -= 1;
            // 检查当前窗口
            if diff.iter().all(|&x| x == 0) {
                result.push((i - m + 1) as i32);
            }
        }

        result
    }
}
```
