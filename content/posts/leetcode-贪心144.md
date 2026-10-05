---
title: "leetcode-贪心144"
date: 2026-10-03T12:55:43+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 交换后字典序最小的字符串

给你一个仅由数字组成的字符串 s，在最多交换一次 相邻 且具有相同 奇偶性 的数字后，返回可以得到的字典序最小的字符串。

如果两个数字都是奇数或都是偶数，则它们具有相同的奇偶性。例如，5 和 9、2 和 4 奇偶性相同，而 6 和 9 奇偶性不同。


```
impl Solution {
    pub fn get_smallest_string(s: String) -> String {
        // 只能交换一次相邻且奇偶性相同的数字。
        // 从左到右找第一对「前 > 后且同奇偶」的相邻数字，交换它们即可得到最小字典序。
        let mut b = s.into_bytes();

        for i in 0..b.len() - 1 {
            // 数字字符的奇偶性等价于其 ASCII 码的奇偶性
            if b[i] > b[i + 1] && (b[i] ^ b[i + 1]) & 1 == 0 {
                b.swap(i, i + 1);
                break;
            }
        }

        String::from_utf8(b).unwrap()
    }
}
```
