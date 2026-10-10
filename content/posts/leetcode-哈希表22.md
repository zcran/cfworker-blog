---
title: "leetcode-哈希表22"
date: 2026-10-09T20:07:56+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 亲密字符串

给你两个字符串 s 和 goal ，只要我们可以通过交换 s 中的两个字母得到与 goal 相等的结果，就返回 true ；否则返回 false 。

交换字母的定义是：取两个下标 i 和 j （下标从 0 开始）且满足 i != j ，接着交换 s[i] 和 s[j] 处的字符。

例如，在 "abcd" 中交换下标 0 和下标 2 的元素可以生成 "cbad" 。


```
impl Solution {
    pub fn buddy_strings(s: String, goal: String) -> bool {
        // 长度不同一定无法通过一次交换相等。
        if s.len() != goal.len() {
            return false;
        }

        let (a, b) = (s.as_bytes(), goal.as_bytes());

        if a == b {
            // 已相等：必须存在重复字符，才能交换两个相同字母而不改变字符串。
            let mut cnt = [0u8; 26];
            for &c in a {
                let i = (c - b'a') as usize;
                cnt[i] += 1;
                if cnt[i] > 1 {
                    return true;
                }
            }
            false
        } else {
            // 不相等：恰好有两个位置不同，且交叉相等（a[i]==b[j] 且 a[j]==b[i]）。
            let mut diff = (0..a.len()).filter(|&i| a[i] != b[i]);
            match (diff.next(), diff.next(), diff.next()) {
                (Some(i), Some(j), None) => a[i] == b[j] && a[j] == b[i],
                _ => false,
            }
        }
    }
}
```
