---
title: "leetcode-数学55"
date: 2026-10-07T20:39:32+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 判断能否在给定时间到达单元格

给你四个整数 sx、sy、fx、fy  以及一个 非负整数 t 。

在一个无限的二维网格中，你从单元格 (sx, sy) 开始出发。每一秒，你 必须 移动到任一与之前所处单元格相邻的单元格中。

如果你能在 恰好 t 秒 后到达单元格 (fx, fy) ，返回 true ；否则，返回  false 。

单元格的 相邻单元格 是指该单元格周围与其至少共享一个角的 8 个单元格。你可以多次访问同一个单元格。


```
impl Solution {
    pub fn is_reachable_at_time(sx: i32, sy: i32, fx: i32, fy: i32, t: i32) -> bool {
        let dx = (fx - sx).abs();
        let dy = (fy - sy).abs();
        let min_steps = dx.max(dy); // 切比雪夫距离：8 邻域下至少需要的步数

        // 起点即终点时，若 t == 1 无法恰好用 1 秒停留（必须移动），否则可以来回走消耗多余步数。
        if dx == 0 && dy == 0 {
            return t != 1;
        }

        min_steps <= t
    }
}
```
