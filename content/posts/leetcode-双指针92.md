---
title: "leetcode-双指针92"
date: 2026-09-05T09:53:06+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 不重叠回文子字符串的最大数目

给你一个字符串 s 和一个 正 整数 k 。

从字符串 s 中选出一组满足下述条件且 不重叠 的子字符串：

每个子字符串的长度 至少 为 k 。
每个子字符串是一个 回文串 。

返回最优方案中能选择的子字符串的 最大 数目。

子字符串 是字符串中一个连续的字符序列。


```
impl Solution {
    pub fn max_palindromes(s: String, k: i32) -> i32 {
        let s = s.as_bytes();
        let n = s.len();
        let k = k as usize;
        let mut ans = 0;
        let mut start = 0; // 下一个可选子串的起始位置

        // 枚举每个可能的右端点
        for r in k - 1..n {
            // 优先尝试长度恰好为 k 的回文
            let l = r + 1 - k;
            if l >= start && Self::is_palindrome(&s[l..=r]) {
                ans += 1;
                start = r + 1;
                continue;
            }

            // 再尝试长度恰好为 k+1 的回文（处理奇偶中心差异）
            if r >= k {
                let l = r - k;
                if l >= start && Self::is_palindrome(&s[l..=r]) {
                    ans += 1;
                    start = r + 1;
                }
            }
        }

        ans
    }

    /// 判断字节切片是否为回文
    fn is_palindrome(s: &[u8]) -> bool {
        let (mut i, mut j) = (0, s.len());
        while i < j {
            j -= 1;
            if s[i] != s[j] {
                return false;
            }
            i += 1;
        }
        true
    }
}
```
