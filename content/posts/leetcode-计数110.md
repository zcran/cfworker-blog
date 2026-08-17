---
title: "leetcode-计数110"
date: 2026-08-01T10:40:57+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 奇偶频次间的最大差值 I

给你一个由小写英文字母组成的字符串 s。

请你找出字符串中两个字符 a1 和 a2 的出现频次之间的 最大 差值 diff = freq(a1) - freq(a2)，这两个字符需要满足：

a1 在字符串中出现 奇数次 。
a2 在字符串中出现 偶数次 。

返回 最大 差值。


```
impl Solution {
    pub fn max_difference(s: String) -> i32 {
        let mut cnt = [0i32; 26];
        for b in s.bytes() { cnt[(b - b'a') as usize] += 1; }

        let mut max_odd = 1;
        let mut min_even = s.len() as i32;
        for &v in &cnt {
            if v == 0 { continue; }
            if v & 1 == 1 {
                max_odd = max_odd.max(v);
            } else {
                min_even = min_even.min(v);
            }
        }
        max_odd - min_even
    }
}
```
