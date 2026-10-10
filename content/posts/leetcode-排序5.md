---
title: "leetcode-排序5"
date: 2026-10-05T00:34:59+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 有效的字母异位词

给定两个字符串 s 和 t ，编写一个函数来判断 t 是否是 s 的 字母异位词。


```
impl Solution {
    /// 判断 t 是否为 s 的字母异位词
    ///
    /// 思路：字母异位词意味着两字符串字符种类与数量完全一致。
    /// 先用长度做快速否定，再用长度 26 的计数数组做增减平衡，
    /// 最终全零即互为异位词。
    pub fn is_anagram(s: String, t: String) -> bool {
        // 长度不同必然不是异位词
        if s.len() != t.len() {
            return false;
        }

        // 题目限定小写英文字母，用栈上定长数组代替 HashMap
        let mut cnt = [0i32; 26];
        for (a, b) in s.bytes().zip(t.bytes()) {
            cnt[(a - b'a') as usize] += 1;
            cnt[(b - b'a') as usize] -= 1;
        }

        // 所有计数归零，说明两串字符分布一致
        cnt.iter().all(|&x| x == 0)
    }
}
```
