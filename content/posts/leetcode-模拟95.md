---
title: "leetcode-模拟95"
date: 2026-08-08T11:31:14+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 最大字符串配对数目

给你一个下标从 0 开始的数组 words ，数组中包含 互不相同 的字符串。

如果字符串 words[i] 与字符串 words[j] 满足以下条件，我们称它们可以匹配：

字符串 words[i] 等于 words[j] 的反转字符串。

0 <= i < j < words.length

请你返回数组 words 中的 最大 匹配数目。

注意，每个字符串最多匹配一次。


```
use std::collections::HashSet;

impl Solution {
    /// 返回数组中可以组成「反转配对」的最大数目。
    ///
    /// 思路：遍历每个字符串，检查它的反转是否已经在之前出现过。
    /// - 若出现过，说明找到了一对，计数加一；
    /// - 若未出现，将当前字符串存入集合，供后续匹配。
    /// 每个字符串最多参与一次配对，因此用 HashSet 记录「待匹配」的字符串即可。
    pub fn maximum_number_of_string_pairs(words: Vec<String>) -> i32 {
        let mut seen = HashSet::new();
        let mut pairs = 0;

        for word in words {
            let reversed: String = word.chars().rev().collect();

            if seen.contains(&reversed) {
                // 反转串已存在于集合中，说明之前有个字符串与当前串互为反转
                pairs += 1;
            } else {
                // 当前串尚未找到配对，先存入集合等待后续匹配
                seen.insert(word);
            }
        }

        pairs
    }
}
```
