---
title: "leetcode-位运算35"
date: 2026-09-24T10:33:36+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 统计追加字母可以获得的单词数

给你两个下标从 0 开始的字符串数组 startWords 和 targetWords 。每个字符串都仅由 小写英文字母 组成。

对于 targetWords 中的每个字符串，检查是否能够从 startWords 中选出一个字符串，执行一次 转换操作 ，得到的结果与当前 targetWords 字符串相等。

转换操作 如下面两步所述：

1. 追加 任何 不存在 于当前字符串的任一小写字母到当前字符串的末尾。
    · 例如，如果字符串为 "abc" ，那么字母 'd'、'e' 或 'y' 都可以加到该字符串末尾，但 'a' 就不行。如果追加的是 'd' ，那么结果字符串为 "abcd" 。

2. 重排 新字符串中的字母，可以按 任意 顺序重新排布字母。
    · 例如，"abcd" 可以重排为 "acbd"、"bacd"、"cbda"，以此类推。注意，它也可以重排为 "abcd" 自身。

找出 targetWords 中有多少字符串能够由 startWords 中的 任一 字符串执行上述转换操作获得。返回 targetWords 中这类 字符串的数目 。

注意：你仅能验证 targetWords 中的字符串是否可以由 startWords 中的某个字符串经执行操作获得。startWords  中的字符串在这一过程中 不 发生实际变更。


```
use std::collections::HashSet;

impl Solution {
    /// 统计 targetWords 中能由 startWords 中某字符串经一次转换得到的数目。
    ///
    /// 转换本质：在某个 startWords 字符串中追加一个不存在的字母，再任意重排。
    /// 由于重排不改变字母集合，字符串仅需用 26 位掩码表示。
    /// 因此对每个 targetWord，枚举删掉其中一个字母后的掩码，
    /// 若该掩码在 startWords 的掩码集合中，则说明可由该 startWord 追加该字母得到。
    pub fn word_count(start_words: Vec<String>, target_words: Vec<String>) -> i32 {
        // 将 startWords 全部转为字母掩码集合
        let start: HashSet<u32> = start_words
            .iter()
            .map(|s| s.bytes().fold(0u32, |mask, b| mask | 1 << (b - b'a')))
            .collect();

        let mut count = 0;
        for word in &target_words {
            // targetWord 的完整字母掩码
            let mask = word.bytes().fold(0u32, |m, b| m | 1 << (b - b'a'));

            // 尝试删除每个字母，若剩余掩码存在于 start 集合中则合法
            for b in word.bytes() {
                if start.contains(&(mask ^ (1 << (b - b'a')))) {
                    count += 1;
                    break;
                }
            }
        }

        count
    }
}
```
