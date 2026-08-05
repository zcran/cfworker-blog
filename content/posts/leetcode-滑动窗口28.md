---
title: "leetcode-滑动窗口28"
date: 2026-07-18T11:02:30+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 尽可能使字符串相等

给你两个长度相同的字符串，s 和 t。

将 s 中的第 i 个字符变到 t 中的第 i 个字符需要 |s[i] - t[i]| 的开销（开销可能为 0），也就是两个字符的 ASCII 码值的差的绝对值。

用于变更字符串的最大预算是 maxCost。在转化字符串时，总开销应当小于等于该预算，这也意味着字符串的转化可能是不完全的。

如果你可以将 s 的子字符串转化为它在 t 中对应的子字符串，则返回可以转化的最大长度。

如果 s 中没有子字符串可以转化成 t 中对应的子字符串，则返回 0。


```
impl Solution {
    pub fn equal_substring(s: String, t: String, max_cost: i32) -> i32 {
        let s = s.as_bytes();
        let t = t.as_bytes();
        let n = s.len();

        // 计算每个位置的转换开销
        let mut costs = Vec::with_capacity(n);
        for i in 0..n {
            costs.push((s[i] as i32 - t[i] as i32).abs());
        }

        let mut max_len = 0;
        let mut cost = 0;
        let mut left = 0;

        // 滑动窗口：右指针 right 不断右移
        for right in 0..n {
            cost += costs[right];

            // 如果开销超过预算，移动左指针直到满足条件
            while cost > max_cost {
                cost -= costs[left];
                left += 1;
            }

            // 更新最大长度
            max_len = max_len.max(right - left + 1);
        }

        max_len as i32
    }
}
```
