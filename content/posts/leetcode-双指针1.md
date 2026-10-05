---
title: "leetcode-双指针1"
date: 2026-09-05T09:53:00+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 最长回文子串

给你一个字符串 s，找到 s 中最长的 回文 子串。

```
impl Solution {
    /// 中心扩展法寻找最长回文子串
    /// 时间复杂度 O(n²)，空间复杂度 O(1)
    /// 以每个字符（或字符间隙）为中心向两边扩展，记录最长回文
    pub fn longest_palindrome(s: String) -> String {
        let chars: Vec<char> = s.chars().collect();
        let n = chars.len();
        if n < 2 {
            return s;
        }

        let mut start = 0;
        let mut max_len = 1;

        // 尝试以每个位置为中心扩展
        for i in 0..n {
            // 奇数长度回文：中心为单个字符
            let (left1, right1) = expand(&chars, i, i);
            // 偶数长度回文：中心为两个字符之间
            let (left2, right2) = expand(&chars, i, i + 1);

            // 更新最长回文
            if right1 - left1 + 1 > max_len {
                start = left1;
                max_len = right1 - left1 + 1;
            }
            if right2 - left2 + 1 > max_len {
                start = left2;
                max_len = right2 - left2 + 1;
            }
        }

        chars[start..start + max_len].iter().collect()
    }
}

/// 从中心向两边扩展，返回回文子串的左右边界（闭区间）
fn expand(chars: &[char], mut left: usize, mut right: usize) -> (usize, usize) {
    while right < chars.len() && chars[left] == chars[right] {
        // 防止 left 下溢
        if left == 0 {
            return (0, right);
        }
        left -= 1;
        right += 1;
    }
    (left + 1, right - 1)
}
```
