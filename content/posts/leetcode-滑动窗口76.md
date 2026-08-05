---
title: "leetcode-滑动窗口76"
date: 2026-07-18T11:02:33+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 最短且字典序最小的美丽子字符串

给你一个二进制字符串 s 和一个正整数 k 。

如果 s 的某个子字符串中 1 的个数恰好等于 k ，则称这个子字符串是一个 美丽子字符串 。

令 len 等于 最短 美丽子字符串的长度。

返回长度等于 len 且字典序 最小 的美丽子字符串。如果 s 中不含美丽子字符串，则返回一个 空 字符串。

对于相同长度的两个字符串 a 和 b ，如果在 a 和 b 出现不同的第一个位置上，a 中该位置上的字符严格大于 b 中的对应字符，则认为字符串 a 字典序 大于 字符串 b 。

例如，"abcd" 的字典序大于 "abcc" ，因为两个字符串出现不同的第一个位置对应第四个字符，而 d 大于 c 。


```
impl Solution {
    pub fn shortest_beautiful_substring(s: String, k: i32) -> String {
        let s = s.as_bytes();
        let k = k as usize;
        let mut ans = String::new();
        let mut left = 0;
        let mut ones = 0;

        for right in 0..s.len() {
            // 右边界扩展：加入当前字符
            ones += (s[right] - b'0') as usize;

            // 左边界收缩条件：
            // 1. 1 的个数超过 k
            // 2. 左边界是 0（可以删除而不影响 1 的个数）
            while ones > k || (left < right && s[left] == b'0') {
                ones -= (s[left] - b'0') as usize;
                left += 1;
            }

            // 检查当前窗口是否满足条件
            if ones == k {
                let candidate = &s[left..=right];
                // 更新答案：优先选择更短的，若等长则选字典序更小的
                if ans.is_empty()
                    || candidate.len() < ans.len()
                    || (candidate.len() == ans.len() && candidate < ans.as_bytes())
                {
                    ans = String::from_utf8(candidate.to_vec()).unwrap();
                }
            }
        }

        ans
    }
}
```
