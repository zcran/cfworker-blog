---
title: "leetcode-计数36"
date: 2026-08-01T10:40:52+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 满足三条件之一需改变的最少字符数

给你两个字符串 a 和 b ，二者均由小写字母组成。一步操作中，你可以将 a 或 b 中的 任一字符 改变为 任一小写字母 。

操作的最终目标是满足下列三个条件 之一 ：

a 中的 每个字母 在字母表中 严格小于 b 中的 每个字母 。
b 中的 每个字母 在字母表中 严格小于 a 中的 每个字母 。
a 和 b 都 由 同一个 字母组成。

返回达成目标所需的 最少 操作数。




```
impl Solution {
    pub fn min_characters(a: String, b: String) -> i32 {
        // 统计两个字符串中各字母出现次数
        let (mut ca, mut cb) = ([0; 26], [0; 26]);
        for &ch in a.as_bytes() { ca[(ch - b'a') as usize] += 1; }
        for &ch in b.as_bytes() { cb[(ch - b'a') as usize] += 1; }

        let (na, nb) = (a.len() as i32, b.len() as i32);
        let mut ans = na + nb;

        // 情况3：a和b都变成同一个字母
        for i in 0..26 {
            ans = ans.min(na + nb - ca[i] - cb[i]);
        }

        // 情况1：a中所有字母 < b中所有字母
        // 情况2：b中所有字母 < a中所有字母
        let (mut prefix_a, mut prefix_b) = (0, 0);
        for i in 0..25 {
            prefix_a += ca[i];
            prefix_b += cb[i];
            // 将a中 >= i+1 的字母改小，将b中 <= i 的字母改大
            ans = ans.min(na - prefix_a + prefix_b);
            // 将b中 >= i+1 的字母改小，将a中 <= i 的字母改大
            ans = ans.min(nb - prefix_b + prefix_a);
        }

        ans
    }
}
```
