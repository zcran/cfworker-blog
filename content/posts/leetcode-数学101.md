---
title: "leetcode-数学101"
date: 2026-10-07T20:39:35+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 象到达目标格子的最少移动步数

给你一个 8 x 8 的棋盘，行和列的下标从 1 开始。

给你一个数组 source = [sr, sc]，表示 象 的起始位置，以及一个数组 target = [tr, tc]。在一步移动中，象可以在棋盘范围内沿着单个 对角线 方向移动任意数量的格子。

返回象 恰好 到达 target 位置所需的 最少 移动次数。如果它永远无法到达 target，则返回 -1。


```
impl Solution {
    pub fn min_bishop_moves(source: Vec<i32>, target: Vec<i32>) -> i32 {
        let (sx, sy) = (source[0], source[1]);
        let (tx, ty) = (target[0], target[1]);

        // 起点和终点同色（横纵坐标之和奇偶性相同）才可达
        if (sx + sy) % 2 != (tx + ty) % 2 {
            return -1;
        }

        // 同色时：起点等于终点返回 0；
        // 若在同一对角线上（斜率 ±1）可 1 步到达；否则 2 步
        if sx == tx && sy == ty {
            0
        } else if sx + sy == tx + ty || sx - sy == tx - ty {
            1
        } else {
            2
        }
    }
}
```
