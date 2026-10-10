---
title: "leetcode-字符串60"
date: 2026-10-10T12:56:56+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 最长平衡子字符串

给你一个仅由 0 和 1 组成的二进制字符串 s 。

如果子字符串中 所有的 0 都在 1 之前 且其中 0 的数量等于 1 的数量，则认为 s 的这个子字符串是平衡子字符串。请注意，空子字符串也视作平衡子字符串。

返回  s 中最长的平衡子字符串长度。

子字符串是字符串中的一个连续字符序列。


```
impl Solution {
    pub fn find_the_longest_balanced_substring(s: String) -> i32 {
        let b = s.as_bytes();
        let (mut ans, mut zeros, mut ones) = (0, 0, 0);

        for &c in b {
            if c == b'0' {
                // 遇到 0：若前面刚统计完一段 1，则重新开始计数 0
                if ones > 0 {
                    zeros = 0;
                    ones = 0;
                }
                zeros += 1;
            } else {
                ones += 1;
                // 每遇到一个 1，就能与一个 0 配对
                if ones <= zeros {
                    ans = ans.max(ones * 2);
                }
            }
        }

        ans
    }
}
```
