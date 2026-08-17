---
title: "leetcode-计数104"
date: 2026-08-01T10:40:56+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 操作后字符串的最短长度

给你一个字符串 s 。

你需要对 s 执行以下操作 任意 次：

选择一个下标 i ，满足 s[i] 左边和右边都 至少 有一个字符与它相同。
删除 i 左边 离它 最近 的 s[i] 字符。
删除 i 右边 离它 最近 的 s[i] 字符。

请你返回执行完所有操作后， s 的 最短 长度。


```
impl Solution {
    pub fn minimum_length(s: String) -> i32 {
        let mut cnt = [0; 26];
        for b in s.bytes() {
            cnt[(b - b'a') as usize] += 1;
        }

        // 每个字符：奇数次剩1个，偶数次剩2个
        cnt.iter()
            .filter(|&&c| c > 0)
            .map(|&c| if c % 2 == 1 { 1 } else { 2 })
            .sum()
    }
}
```
