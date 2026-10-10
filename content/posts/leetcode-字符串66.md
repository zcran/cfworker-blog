---
title: "leetcode-字符串66"
date: 2026-10-10T12:56:57+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 使二进制字符串变美丽的最少修改次数

给你一个长度为偶数下标从 0 开始的二进制字符串 s 。

如果可以将一个字符串分割成一个或者更多满足以下条件的子字符串，那么我们称这个字符串是 美丽的 ：

每个子字符串的长度都是 偶数 。
每个子字符串都 只 包含 1 或 只 包含 0 。

你可以将 s 中任一字符改成 0 或者 1 。

请你返回让字符串 s 美丽的 最少 字符修改次数。




```
impl Solution {
    pub fn min_changes(s: String) -> i32 {
        // 每两个字符一组，若不相同则需修改其中一个
        s.as_bytes()
            .chunks(2)
            .filter(|pair| pair[0] != pair[1])
            .count() as i32
    }
}
```
