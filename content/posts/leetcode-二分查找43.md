---
title: "leetcode-二分查找43"
date: 2026-09-28T12:29:13+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 放置盒子

有一个立方体房间，其长度、宽度和高度都等于 n 个单位。请你在房间里放置 n 个盒子，每个盒子都是一个单位边长的立方体。放置规则如下：

你可以把盒子放在地板上的任何地方。

如果盒子 x 需要放置在盒子 y 的顶部，那么盒子 y 竖直的四个侧面都 必须 与另一个盒子或墙相邻。

给你一个整数 n ，返回接触地面的盒子的 最少 可能数量。


```
impl Solution {
    pub fn minimum_boxes(n: i32) -> i32 {
        let n = n as i64;

        // 先求最大的 k，使得金字塔层 1..=k 的总盒数 k*(k+1)*(k+2)/6 不超过 n
        let mut k = (6.0 * n as f64).cbrt() as i64;
        while (k + 1) * (k + 2) * (k + 3) / 6 <= n {
            k += 1;
        }
        while k * (k + 1) * (k + 2) / 6 > n {
            k -= 1;
        }

        // 完整金字塔消耗的盒数，及其底层盒数 1+2+...+k
        let used = k * (k + 1) * (k + 2) / 6;
        let base = k * (k + 1) / 2;
        let rest = n - used;

        // 剩余盒子在底层新增：能放满 m 层三角形需要 m*(m+1)/2 个
        let mut m = (2.0 * rest as f64).sqrt() as i64;
        while (m + 1) * (m + 2) / 2 <= rest {
            m += 1;
        }
        while m * (m + 1) / 2 > rest {
            m -= 1;
        }

        (base + m + if m * (m + 1) / 2 < rest { 1 } else { 0 }) as i32
    }
}
```
