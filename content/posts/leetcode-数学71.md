---
title: "leetcode-数学71"
date: 2026-10-07T20:39:33+08:00
tags: ["leetcode", "数学"]
draft: false
---


##  找到最近的人

给你三个整数 x、y 和 z，表示数轴上三个人的位置：

x 是第 1 个人的位置。
y 是第 2 个人的位置。
z 是第 3 个人的位置，第 3 个人 不会移动 。

第 1 个人和第 2 个人以 相同 的速度向第 3 个人移动。

判断谁会 先 到达第 3 个人的位置：

如果第 1 个人先到达，返回 1 。
如果第 2 个人先到达，返回 2 。
如果两个人同时到达，返回 0 。

根据上述规则返回结果。


```
impl Solution {
    pub fn find_closest(x: i32, y: i32, z: i32) -> i32 {
        // 相同速度下，距离第 3 个人越近越先到达。
        // 比较 |x - z| 与 |y - z|：小者先到，相等则同时到达。
        let (dx, dy) = ((x - z).abs(), (y - z).abs());

        match dx.cmp(&dy) {
            std::cmp::Ordering::Less => 1,    // 第 1 个人更近
            std::cmp::Ordering::Greater => 2, // 第 2 个人更近
            std::cmp::Ordering::Equal => 0,   // 距离相同，同时到达
        }
    }
}
```
