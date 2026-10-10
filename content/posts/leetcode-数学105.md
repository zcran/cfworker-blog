---
title: "leetcode-数学105"
date: 2026-10-07T20:39:35+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 皇后到达目标格子的最少移动步数

一个 8 x 8 的空棋盘，其行和列的下标从 1 开始。

给你一个数组 source = [sr, sc] 表示 皇后 的初始位置，以及一个数组 target = [tr, tc] 表示目标位置。

在一步移动中，皇后可以在棋盘范围内，沿着单条 行 、 列 或 对角线 移动一个或多个方格。

返回皇后移动到 恰好 落在 target 位置所需的 最小 移动次数。


```
impl Solution {
    pub fn min_queen_moves(source: Vec<i32>, target: Vec<i32>) -> i32 {
        let (sr, sc) = (source[0], source[1]);
        let (tr, tc) = (target[0], target[1]);

        // 起点即终点
        if sr == tr && sc == tc {
            0
        }
        // 同行、同列或同对角线，可一步到达
        else if sr == tr || sc == tc || sr + sc == tr + tc || sr - sc == tr - tc {
            1
        }
        // 皇后在 8x8 棋盘上最多两步可达任意格
        else {
            2
        }
    }
}
```
