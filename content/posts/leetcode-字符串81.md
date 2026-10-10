---
title: "leetcode-字符串81"
date: 2026-10-10T12:56:58+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 将一个字符串排序的最小操作次数

给你一个由小写英文字母组成的字符串 s。

在一次操作中，你可以选择 s 的任意 子字符串（但 不能 是整个字符串），并将其按 非降序字母顺序 进行 排序。

返回使 s 按 非降序 排列所需的 最小 操作次数。如果无法做到，则返回 -1。




```
impl Solution {
    pub fn min_operations(s: String) -> i32 {
        let b = s.as_bytes();
        let n = b.len();

        // 已经非降序，无需操作
        if b.windows(2).all(|w| w[0] <= w[1]) {
            return 0;
        }
        // 长度为 2 且乱序，无法通过非整体排序修复
        if n == 2 {
            return -1;
        }

        // 最小和最大字符
        let mn = *b.iter().min().unwrap();
        let mx = *b.iter().max().unwrap();

        // 最小值在开头或最大值在末尾，排序其余部分即可一步完成
        if b[0] == mn || b[n - 1] == mx {
            return 1;
        }

        // 最小值或最大值出现在中间，可两步完成
        if b[1..n - 1].iter().any(|&c| c == mn || c == mx) {
            return 2;
        }

        3
    }
}
```
