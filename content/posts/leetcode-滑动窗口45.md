---
title: "leetcode-滑动窗口45"
date: 2026-07-18T11:02:31+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 长度为三且各字符不同的子字符串

如果一个字符串不含有任何重复字符，我们称这个字符串为 好 字符串。

给你一个字符串 s ，请你返回 s 中长度为 3 的 好子字符串 的数量。

注意，如果相同的好子字符串出现多次，每一次都应该被记入答案之中。

子字符串 是一个字符串中连续的字符序列。


```
impl Solution {
    pub fn count_good_substrings(s: String) -> i32 {
        let bytes = s.as_bytes();
        let n = bytes.len();

        if n < 3 {
            return 0;
        }

        let mut count = 0;
        for i in 0..=n - 3 {
            let a = bytes[i];
            let b = bytes[i + 1];
            let c = bytes[i + 2];

            if a != b && b != c && a != c {
                count += 1;
            }
        }

        count
    }
}
```
