---
title: "leetcode-数学7"
date: 2026-10-07T20:39:28+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 到达终点

给定四个整数 sx , sy ，tx 和 ty，如果通过一系列的转换可以从起点 (sx, sy) 到达终点 (tx, ty)，则返回 true，否则返回 false。

从点 (x, y) 可以转换到 (x, x+y)  或者 (x+y, y)。


```
impl Solution {
    /// 判断能否从 (sx, sy) 通过 (x+y, y) 或 (x, x+y) 变换到达 (tx, ty)。
    ///
    /// 正向变换会使坐标增大，因此从终点反向推回起点：
    ///   - 若 tx > ty，则最后一步必为 (tx - ty, ty)，可连续减去多次用取模加速；
    ///   - 若 ty > tx，则最后一步必为 (tx, ty - tx)。
    /// 直到某一坐标不再大于起点对应坐标为止。
    ///
    /// 此时若坐标与起点对应相等，则剩余的另一维差值必须是该维坐标的整数倍
    /// （相当于沿一个方向反复加）。
    pub fn reaching_points(sx: i32, sy: i32, mut tx: i32, mut ty: i32) -> bool {
        // 反向逼近，直到至少一维不再大于起点对应维
        while tx > sx && ty > sy {
            if tx > ty {
                tx %= ty;
            } else {
                ty %= tx;
            }
        }

        // 此时至少有一维已无法再减，判断另一维差值是当前维的整数倍
        (tx == sx && ty >= sy && (ty - sy) % tx == 0)
            || (ty == sy && tx >= sx && (tx - sx) % ty == 0)
    }
}
```
