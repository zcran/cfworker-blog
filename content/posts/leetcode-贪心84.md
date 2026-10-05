---
title: "leetcode-贪心84"
date: 2026-10-03T12:55:39+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 使用特殊打字机键入单词的最少时间

有一个特殊打字机，它由一个 圆盘 和一个 指针 组成， 圆盘上标有小写英文字母 'a' 到 'z'。只有 当指针指向某个字母时，它才能被键入。指针 初始时 指向字符 'a' 。

每一秒钟，你可以执行以下操作之一：

将指针 顺时针 或者 逆时针 移动一个字符。
键入指针 当前 指向的字符。

给你一个字符串 word ，请你返回键入 word 所表示单词的 最少 秒数 。

```
impl Solution {
    pub fn min_time_to_type(word: String) -> i32 {
        let mut pos = 0i32;   // 指针当前位置，'a' 为 0
        let mut time = 0;

        for b in word.bytes() {
            let target = (b - b'a') as i32;
            // 环上两点的最短距离：正向距离与 26 - 正向距离取较小
            let diff = (target - pos).abs();
            time += diff.min(26 - diff) + 1; // +1 为键入耗时
            pos = target;
        }
        time
    }
}
```
