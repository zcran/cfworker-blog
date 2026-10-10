---
title: "leetcode-数学96"
date: 2026-10-07T20:39:35+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 偶数次骑士移动

给你两个整数数组 start 和 target，每个数组的形式均为 [x, y]，表示标准 8 x 8 国际象棋棋盘上的一个格子。

如果骑士可以用 偶数 次移动从 start 到达 target，则返回 true；否则返回 false。

注意：骑士的一次合法移动是：沿一个方向移动两格，再沿与其垂直的方向移动一格。下图展示了骑士从一个格子出发时所有 8 种可能的移动方式。


```
impl Solution {
    pub fn can_reach(start: Vec<i32>, target: Vec<i32>) -> bool {
        // 骑士每次移动都会改变所在格子的颜色（因为横纵坐标之和的奇偶性必变），
        // 所以偶数次移动后颜色不变，即起点和终点的横纵坐标之和奇偶性相同
        (start[0] + start[1]) % 2 == (target[0] + target[1]) % 2
    }
}
```
