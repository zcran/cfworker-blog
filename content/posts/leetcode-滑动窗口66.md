---
title: "leetcode-滑动窗口66"
date: 2026-07-18T11:02:33+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 找到最长的半重复子字符串

给你一个下标从 0 开始的字符串 s ，这个字符串只包含 0 到 9 的数字字符。

如果一个字符串 t 中至多有一对相邻字符是相等的，那么称这个字符串 t 是 半重复的 。例如，"0010" 、"002020" 、"0123" 、"2002" 和 "54944" 是半重复字符串，而 "00101022" （相邻的相同数字对是 00 和 22）和 "1101234883" （相邻的相同数字对是 11 和 88）不是半重复字符串。

请你返回 s 中最长 半重复 子字符串 的长度。


```
impl Solution {
    pub fn longest_semi_repetitive_substring(s: String) -> i32 {
        let s = s.as_bytes();
        let n = s.len();
        let mut ans = 0;
        // 滑动窗口 [left, right] 内相同相邻字符对的数量
        let mut same_pairs = 0;
        let mut left = 0;

        for right in 0..n {
            // 加入右边界字符时，检查是否产生新的相同相邻对
            if right > 0 && s[right] == s[right - 1] {
                same_pairs += 1;
            }

            // 如果相同相邻对超过 1 个，移动左边界直到合法
            while same_pairs > 1 {
                // 移除左边界字符时，检查是否减少了一个相同相邻对
                if left < right && s[left] == s[left + 1] {
                    same_pairs -= 1;
                }
                left += 1;
            }

            ans = ans.max(right - left + 1);
        }

        ans as i32
    }
}
```
