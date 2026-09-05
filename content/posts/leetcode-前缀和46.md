---
title: "leetcode-前缀和46"
date: 2026-08-25T10:02:48+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 长度为 3 的不同回文子序列

给你一个字符串 s ，返回 s 中 长度为 3 的不同回文子序列 的个数。

即便存在多种方法来构建相同的子序列，但相同的子序列只计数一次。

回文 是正着读和反着读一样的字符串。

子序列 是由原字符串删除其中部分字符（也可以不删除）且不改变剩余字符之间相对顺序形成的一个新字符串。

例如，"ace" 是 "abcde" 的一个子序列。


```
impl Solution {
    /// 计算长度为 3 的不同回文子序列的个数
    ///
    /// 思路：枚举首尾字符，统计中间出现的不同字符
    pub fn count_palindromic_subsequence(s: String) -> i32 {
        let bytes = s.as_bytes();
        let n = bytes.len();

        // 记录每个字符第一次和最后一次出现的位置
        let mut first = vec![n; 26];
        let mut last = vec![0; 26];

        for (i, &b) in bytes.iter().enumerate() {
            let idx = (b - b'a') as usize;
            if i < first[idx] {
                first[idx] = i;
            }
            if i > last[idx] {
                last[idx] = i;
            }
        }

        let mut ans = 0;

        // 枚举首尾字符
        for c in 0..26 {
            let l = first[c];
            let r = last[c];

            // 如果该字符至少出现两次，且中间有字符
            if l + 1 < r {
                // 使用位掩码统计中间的不同字符
                let mut mask = 0u32;
                for &b in &bytes[l + 1..r] {
                    mask |= 1 << (b - b'a');
                }
                ans += mask.count_ones() as i32;
            }
        }

        ans
    }
}
```
