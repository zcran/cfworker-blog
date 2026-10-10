---
title: "leetcode-数学64"
date: 2026-10-07T20:39:32+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 检查棋盘方格颜色是否相同

给你两个字符串 coordinate1 和 coordinate2，代表 8 x 8 国际象棋棋盘上的两个方格的坐标。

如果这两个方格颜色相同，返回 true，否则返回 false。

坐标总是表示有效的棋盘方格。坐标的格式总是先字母（表示列），再数字（表示行）。


```
impl Solution {
    pub fn check_two_chessboards(coordinate1: String, coordinate2: String) -> bool {
        // 棋盘颜色由 (列 + 行) 的奇偶性决定：
        // 两个格子颜色相同 ⇔ 各自 (列+行) 奇偶性相同
        //                ⇔ 两格 (列+行) 之差的奇偶性为偶
        // 直接用 ASCII 码求和判断，无需转成具体列号/行号。
        let a = coordinate1.as_bytes();
        let b = coordinate2.as_bytes();

        let sum_a = a[0] as i32 + a[1] as i32;
        let sum_b = b[0] as i32 + b[1] as i32;

        (sum_a - sum_b) % 2 == 0
    }
}
```
