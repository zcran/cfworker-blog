---
title: "leetcode-枚举53"
date: 2026-07-09T10:05:02+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 捕获黑皇后需要的最少移动次数

现有一个下标从 1 开始的 8 x 8 棋盘，上面有 3 枚棋子。

给你 6 个整数 a 、b 、c 、d 、e 和 f ，其中：

(a, b) 表示白色车的位置。
(c, d) 表示白色象的位置。
(e, f) 表示黑皇后的位置。

假定你只能移动白色棋子，返回捕获黑皇后所需的最少移动次数。

请注意：

车可以向垂直或水平方向移动任意数量的格子，但不能跳过其他棋子。
象可以沿对角线方向移动任意数量的格子，但不能跳过其他棋子。
如果车或象能移向皇后所在的格子，则认为它们可以捕获皇后。
皇后不能移动。


```
impl Solution {
    pub fn min_moves_to_capture_the_queen(a: i32, b: i32, c: i32, d: i32, e: i32, f: i32) -> i32 {
        // 检查车能否在1步内捕获皇后
        // 条件：同行或同列，且路径上没有被象阻挡
        let rook_can_capture =
            (a == e && (c != a || !is_between(d, b, f))) || // 同行，且象不在车和皇后之间
            (b == f && (d != b || !is_between(c, a, e)));   // 同列，且象不在车和皇后之间

        if rook_can_capture {
            return 1;
        }

        // 检查象能否在1步内捕获皇后
        // 条件：在同一对角线上，且路径上没有被车阻挡
        let bishop_can_capture =
            (c - e).abs() == (d - f).abs() && // 在同一对角线上
            !is_rook_blocking(a, b, c, d, e, f); // 车不在象和皇后之间

        if bishop_can_capture {
            return 1;
        }

        // 否则需要2步
        2
    }
}

// 判断点 p 是否在区间 [x, y] 之间（严格在内部）
fn is_between(p: i32, x: i32, y: i32) -> bool {
    p > x.min(y) && p < x.max(y)
}

// 判断车 (a,b) 是否在象 (c,d) 到皇后 (e,f) 的对角线路径上
fn is_rook_blocking(a: i32, b: i32, c: i32, d: i32, e: i32, f: i32) -> bool {
    // 车必须在象到皇后的对角线上
    // 即：车到象的行差 == 车到象的列差（绝对值相等）
    (a - c).abs() == (b - d).abs() &&
    // 车必须在象和皇后之间（严格在内部）
    is_between(a, c, e) &&
    is_between(b, d, f)
}
```
