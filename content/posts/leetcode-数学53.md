---
title: "leetcode-数学53"
date: 2026-10-07T20:39:31+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 找出最大的可达成数字

给你两个整数 num 和 t 。如果整数 x 可以在执行下述操作 不超过 t 次的情况下变为与 num 相等，则称其为 可达成数字 ：

每次操作将 x 的值增加或减少 1 ，同时可以选择将 num 的值增加或减少 1 。

返回所有可达成数字中的 最大 值 x。


```
impl Solution {
    pub fn the_maximum_achievable_x(num: i32, t: i32) -> i32 {
        // 每次操作 x 和 num 各变化 ±1，
        // 两者的差距每步最多缩小 2，t 步最多能相差 2t。
        // 因此 x 最大为 num + 2t。
        num + 2 * t
    }
}
```
