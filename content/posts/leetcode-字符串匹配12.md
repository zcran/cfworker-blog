---
title: "leetcode-字符串匹配12"
date: 2026-04-20T20:32:10+08:00
tags: ["leetcode", "字符串匹配"]
draft: false
---


## 统计包含给定前缀的字符串

给你一个字符串数组 words 和一个字符串 pref 。

返回 words 中以 pref 作为 前缀 的字符串的数目。

字符串 s 的 前缀 就是  s 的任一前导连续字符串。


```
impl Solution {
  pub fn prefix_count(words: Vec<String>, pref: String) -> i32 {
     words.iter().filter(|w| w.len()>=pref.len() && w.as_str().starts_with(pref.as_str())).count() as i32
  }
}
```
