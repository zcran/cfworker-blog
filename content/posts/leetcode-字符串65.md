---
title: "leetcode-字符串65"
date: 2026-10-10T12:56:57+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 判断通过操作能否让字符串相等 I

给你两个字符串 s1 和 s2 ，两个字符串的长度都为 4 ，且只包含 小写 英文字母。

你可以对两个字符串中的 任意一个 执行以下操作 任意 次：

选择两个下标 i 和 j 且满足 j - i = 2 ，然后 交换 这个字符串中两个下标对应的字符。

如果你可以让字符串 s1 和 s2 相等，那么返回 true ，否则返回 false 。




```
impl Solution {
    pub fn can_be_equal(s1: String, s2: String) -> bool {
        let a = s1.as_bytes();
        let b = s2.as_bytes();

        // 下标 0 和 2 可以互换，下标 1 和 3 可以互换
        // 因此只需两组内字符集合分别相等
        let (mut g1a, mut g1b) = ([a[0], a[2]], [b[0], b[2]]);
        let (mut g2a, mut g2b) = ([a[1], a[3]], [b[1], b[3]]);
        g1a.sort_unstable();
        g1b.sort_unstable();
        g2a.sort_unstable();
        g2b.sort_unstable();

        g1a == g1b && g2a == g2b
    }
}
```
