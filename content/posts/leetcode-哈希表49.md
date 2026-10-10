---
title: "leetcode-哈希表49"
date: 2026-10-09T20:07:58+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 判断句子是否为全字母句

全字母句 指包含英语字母表中每个字母至少一次的句子。

给你一个仅由小写英文字母组成的字符串 sentence ，请你判断 sentence 是否为 全字母句 。

如果是，返回 true ；否则，返回 false 。



```
impl Solution {
    pub fn check_if_pangram(sentence: String) -> bool {
        // 用 26 位掩码记录出现过的字母，初始 26 位全 1（低 26 位）。
        // 每遇到一个字母就清掉对应位；若 26 个字母都出现，掩码归零。
        sentence
            .bytes()
            .fold((1u32 << 26) - 1, |mask, b| mask & !(1 << (b - b'a')))
            == 0
    }
}
```
