---
title: "leetcode-几何39"
date: 2026-05-11T20:20:56+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 平分正方形


给定两个正方形及一个二维平面。请找出将这两个正方形分割成两半的一条直线。假设正方形顶边和底边与 x 轴平行。

每个正方形的数据square包含3个数值，正方形的左下顶点坐标[X,Y] = [square[0],square[1]]，以及正方形的边长square[2]。所求直线穿过两个正方形会形成4个交点，请返回4个交点形成线段的两端点坐标（两个端点即为4个交点中距离最远的2个点，这2个点所连成的线段一定会穿过另外2个交点）。2个端点坐标[X1,Y1]和[X2,Y2]的返回格式为{X1,Y1,X2,Y2}，要求若X1 != X2，需保证X1 < X2，否则需保证Y1 <= Y2。

若同时有多条直线满足要求，则选择斜率最大的一条计算并返回（与Y轴平行的直线视为斜率无穷大）。

```
impl Solution {
    /// 返回经过两个正方形中心点的直线与两个正方形相交的线段端点。
    /// 每个正方形由 `[x, y, side]` 表示，`(x, y)` 为左下角坐标。
    /// 返回 `[x1, y1, x2, y2]`，其中 `x1 <= x2`。
    pub fn cut_squares(square1: Vec<i32>, square2: Vec<i32>) -> Vec<f64> {
        // 将 i32 边长转换为 f64，便于计算
        let to_f64 = |v: i32| v as f64;
        let (x1, y1, s1) = (to_f64(square1[0]), to_f64(square1[1]), to_f64(square1[2]));
        let (x2, y2, s2) = (to_f64(square2[0]), to_f64(square2[1]), to_f64(square2[2]));

        // 中心点坐标
        let center1 = (x1 + s1 / 2.0, y1 + s1 / 2.0);
        let center2 = (x2 + s2 / 2.0, y2 + s2 / 2.0);

        // 矩形边界（最小/最大 x 和 y）
        let (min_x, max_x) = (x1.min(x2), (x1 + s1).max(x2 + s2));
        let (min_y, max_y) = (y1.min(y2), (y1 + s1).max(y2 + s2));

        // 计算直线方程参数（两点式）
        let (cx1, cy1) = center1;
        let (cx2, cy2) = center2;
        let (dx, dy) = (cx2 - cx1, cy2 - cy1);

        // 辅助函数：根据给定 x 求直线上的 y
        let y_at_x = |x: f64| cy1 + (dy / dx) * (x - cx1);
        // 辅助函数：根据给定 y 求直线上的 x
        let x_at_y = |y: f64| cx1 + (dx / dy) * (y - cy1);

        // 计算线段端点，选择斜率绝对值 ≤1 时以 x 边界确定，否则以 y 边界确定
        let (p1, p2) = if dx == 0.0 {
            // 垂直线，直接用 y 范围
            ((cx1, min_y), (cx1, max_y))
        } else if dy == 0.0 {
            // 水平线，直接用 x 范围
            ((min_x, cy1), (max_x, cy1))
        } else {
            let slope = dy / dx;
            if slope.abs() <= 1.0 {
                // 直线与左右边界相交
                ((min_x, y_at_x(min_x)), (max_x, y_at_x(max_x)))
            } else {
                // 直线与上下边界相交
                ((x_at_y(min_y), min_y), (x_at_y(max_y), max_y))
            }
        };

        // 确保返回的端点按 x 升序排列（若 x 相同则按 y 升序）
        let mut ans = vec![p1.0, p1.1, p2.0, p2.1];
        if p1.0 > p2.0 || (p1.0 == p2.0 && p1.1 > p2.1) {
            ans.swap(0, 2);
            ans.swap(1, 3);
        }
        ans
    }
}
```
