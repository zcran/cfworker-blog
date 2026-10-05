---
title: "leetcode-贪心92"
date: 2026-10-03T12:55:40+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 摧毁小行星

给你一个整数 mass ，它表示一颗行星的初始质量。再给你一个整数数组 asteroids ，其中 asteroids[i] 是第 i 颗小行星的质量。

你可以按 任意顺序 重新安排小行星的顺序，然后让行星跟它们发生碰撞。如果行星碰撞时的质量 大于等于 小行星的质量，那么小行星被 摧毁 ，并且行星会 获得 这颗小行星的质量。否则，行星将被摧毁。

如果所有小行星 都 能被摧毁，请返回 true ，否则返回 false 。


```
impl Solution {
    pub fn asteroids_destroyed(mass: i32, mut asteroids: Vec<i32>) -> bool {
        // 贪心策略：优先撞击质量最小的小行星，以最快速度累积质量
        asteroids.sort_unstable();

        // 用 i64 防止累加过程中溢出
        let mut mass = mass as i64;
        for a in asteroids {
            if mass < a as i64 {
                return false;
            }
            mass += a as i64;
        }
        true
    }
}
```
