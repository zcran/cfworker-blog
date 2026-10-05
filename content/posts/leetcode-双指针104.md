---
title: "leetcode-双指针104"
date: 2026-09-05T09:53:07+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 循环增长使字符串子序列等于另一个字符串

给你一个下标从 0 开始的字符串 str1 和 str2 。

一次操作中，你选择 str1 中的若干下标。对于选中的每一个下标 i ，你将 str1[i] 循环 递增，变成下一个字符。也就是说 'a' 变成 'b' ，'b' 变成 'c' ，以此类推，'z' 变成 'a' 。

如果执行以上操作 至多一次 ，可以让 str2 成为 str1 的子序列，请你返回 true ，否则返回 false 。

注意：一个字符串的子序列指的是从原字符串中删除一些（可以一个字符也不删）字符后，剩下字符按照原本先后顺序组成的新字符串。


```
impl Solution {
    /// 判断能否至多执行一次循环递增操作，使 t 成为 s 的子序列。
    ///
    /// 贪心：双指针遍历 s 和 t，若 s[i] 等于 t[j] 或 s[i] 递增一位后等于 t[j]，
    /// 则匹配成功，j 前进；无论是否匹配，i 都前进。
    /// 最终 j == t.len() 即说明匹配完成。
    /// 时间复杂度 O(n + m)，空间复杂度 O(1)。
    pub fn can_make_subsequence(s: String, t: String) -> bool {
        let s = s.as_bytes();
        let t = t.as_bytes();
        let mut j = 0;

        for &c in s {
            if j == t.len() {
                break;
            }
            // c 本身等于 t[j]，或 c 循环递增一位后等于 t[j]
            if c == t[j] || (c - b'a' + 1) % 26 == t[j] - b'a' {
                j += 1;
            }
        }

        j == t.len()
    }
}
```
