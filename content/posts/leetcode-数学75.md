---
title: "leetcode-数学75"
date: 2026-10-07T20:39:33+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 到达目标点的最小移动次数

给你四个整数 sx、sy、tx 和 ty，表示在一个无限大的二维网格上的两个点 (sx, sy) 和 (tx, ty)。

你的起点是 (sx, sy)。

在任何位置 (x, y)，定义 m = max(x, y)。你可以执行以下两种操作之一：

移动到 (x + m, y)，或者
移动到 (x, y + m)。

返回到达 (tx, ty) 所需的 最小 移动次数。如果无法到达目标点，则返回 -1。


```
impl Solution {
    pub fn min_moves(sx: i32, sy: i32, tx: i32, ty: i32) -> i32 {
        // 逆向思考：从 (tx, ty) 反推到 (sx, sy)。
        // 正向操作 (x, y) -> (x + m, y) 或 (x, y + m)，m = max(x, y)，
        // 逆向时每步把较大的坐标减去较小的坐标（等价于正向加 m）。
        // 若较大坐标 >= 2 * 较小坐标，则正向这一步必然是"较大者翻倍"得来，
        // 因此逆推需整除 2；否则直接做减法。
        let (mut x, mut y) = (tx, ty);
        let (mut sx, mut sy) = (sx, sy);
        let mut ans = 0;

        while x != sx || y != sy {
            ans += 1; // 每进入一次循环代表一次逆向移动（含下面的归零操作）

            if x < sx || y < sy {
                return -1;
            }

            // 两坐标相等时，无法继续用减法/除法拆分，
            // 只能是某一起点分量为 0 的初始情形，尝试归零对应分量。
            if x == y {
                if sy > 0 {
                    x = 0;
                } else {
                    y = 0;
                }
                continue;
            }

            // 保证 x > y，同时交换起点对应分量，保持比较基准一致
            if x < y {
                std::mem::swap(&mut x, &mut y);
                std::mem::swap(&mut sx, &mut sy);
            }

            // x >= 2y 时正向必然经历了 ×2，逆推整除 2；否则做减法
            if x >= y * 2 {
                if x % 2 != 0 {
                    return -1;
                }
                x /= 2;
            } else {
                x -= y;
            }
        }

        ans
    }
}
```
