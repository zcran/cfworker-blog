---
title: "leetcode-计数84"
date: 2026-08-01T10:40:55+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 距离原点最远的点

给你一个长度为 n 的字符串 moves ，该字符串仅由字符 'L'、'R' 和 '_' 组成。字符串表示你在一条原点为 0 的数轴上的若干次移动。

你的初始位置就在原点（0），第 i 次移动过程中，你可以根据对应字符选择移动方向：

· 如果 moves[i] = 'L' 或 moves[i] = '_' ，可以选择向左移动一个单位距离

· 如果 moves[i] = 'R' 或 moves[i] = '_' ，可以选择向右移动一个单位距离

返回在移动 n 次之后，可以到达的距离原点 最远 的点 到原点的距离。




```
impl Solution {
    pub fn furthest_distance_from_origin(moves: String) -> i32 {
        let mut left = 0i32;
        let mut right = 0i32;
        let mut blank = 0i32;

        for &b in moves.as_bytes() {
            match b {
                b'L' => left += 1,
                b'R' => right += 1,
                b'_' => blank += 1,
                _ => unreachable!(),
            }
        }

        // 最远距离 = |L - R| + 空格数
        // 空格可以全部用于弥补差异较大的方向
        (left - right).abs() + blank
    }
}
```
