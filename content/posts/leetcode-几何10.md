---
title: "leetcode-几何10"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 镜面反射

有一个特殊的正方形房间，每面墙上都有一面镜子。除西南角以外，每个角落都放有一个接受器，编号为 0， 1，以及 2。

正方形房间的墙壁长度为 p，一束激光从西南角射出，首先会与东墙相遇，入射点到接收器 0 的距离为 q 。

返回光线最先遇到的接收器的编号（保证光线最终会遇到一个接收器）。



```
impl Solution {
    pub fn mirror_reflection(p: i32, q: i32) -> i32 {
        // 纯函数：最大公约数（迭代但封装）
        let gcd = |mut a: i64, mut b: i64| -> i64 {
            while b != 0 { let t = b; b = a % b; a = t; }
            a
        };
        // 最小公倍数（纯）
        let lcm = |a: i64, b: i64| -> i64 { a / gcd(a, b) * b };

        let (p, q) = (p as i64, q as i64);
        let l = lcm(p, q);
        let (n, m) = ((l / p) as i32, (l / q) as i32); // n:横向格子, m:纵向格子

        match (m % 2, n % 2) {
            (0, _) => 2,
            (1, 1) => 1,
            _ => 0,
        }
    }
}
```
