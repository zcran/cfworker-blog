---
title: "leetcode-二分查找48"
date: 2026-09-28T12:29:13+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 收集足够苹果的最小花园周长

给你一个用无限二维网格表示的花园，每一个 整数坐标处都有一棵苹果树。整数坐标 (i, j) 处的苹果树有 |i| + |j| 个苹果。

你将会买下正中心坐标是 (0, 0) 的一块 正方形土地 ，且每条边都与两条坐标轴之一平行。

给你一个整数 neededApples ，请你返回土地的 最小周长 ，使得 至少 有 neededApples 个苹果在土地 里面或者边缘上。

|x| 的值定义为：

如果 x >= 0 ，那么值为 x
如果 x < 0 ，那么值为 -x


```
impl Solution {
    pub fn minimum_perimeter(needed_apples: i64) -> i64 {
        // 边长为 2n 的正方形（含边界）内苹果总数为 2n(n+1)(2n+1)
        // 目标：找到最小 n 使得总数 >= neededApples
        let mut n = (needed_apples as f64 / 4.0).cbrt() as i64;

        // 修正浮点误差，确保 n 是满足条件的最小值
        while 2 * n * (n + 1) * (2 * n + 1) >= needed_apples {
            n -= 1;
        }
        while 2 * (n + 1) * (n + 2) * (2 * n + 3) < needed_apples {
            n += 1;
        }
        n += 1;

        8 * n
    }
}
```
