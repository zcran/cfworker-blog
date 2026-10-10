---
title: "leetcode-数学35"
date: 2026-10-07T20:39:30+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 判断国际象棋棋盘中一个格子的颜色

给你一个坐标 coordinates ，它是一个字符串，表示国际象棋棋盘中一个格子的坐标(1 - 8, a - h)。

如果所给格子的颜色是白色，请你返回 true，如果是黑色，请返回 false 。

给定坐标一定代表国际象棋棋盘上一个存在的格子。坐标第一个字符是字母，第二个字符是数字。


```
impl Solution {
    pub fn square_is_white(coordinates: String) -> bool {
        // 棋盘颜色由行列下标的奇偶性决定：
        // 字母 a..h 与数字 1..8 奇偶性不同则为白格，相同则为黑格。
        // 利用 ASCII 码的奇偶性判断即可，无需转成具体数值。
        let bytes = coordinates.as_bytes();
        bytes[0] % 2 != bytes[1] % 2
    }
}
```
