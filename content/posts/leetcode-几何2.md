---
title: "leetcode-几何2"
date: 2026-05-11T20:20:54+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 矩形面积

给你 二维 平面上两个 由直线构成且边与坐标轴平行/垂直 的矩形，请你计算并返回两个矩形覆盖的总面积。

每个矩形由其 左下 顶点和 右上 顶点坐标表示：

第一个矩形由其左下顶点 (ax1, ay1) 和右上顶点 (ax2, ay2) 定义。
第二个矩形由其左下顶点 (bx1, by1) 和右上顶点 (bx2, by2) 定义。

```
impl Solution {
    /// 计算两个轴对齐矩形覆盖的总面积。
    ///
    /// 矩形由其左下角 `(x1, y1)` 和右上角 `(x2, y2)` 定义，坐标均为 `i32`。
    /// 总面积 = 矩形 A 面积 + 矩形 B 面积 − 重叠部分面积（若无重叠则重叠面积为 0）。
    pub fn compute_area(
        ax1: i32, ay1: i32, ax2: i32, ay2: i32, // 矩形 A
        bx1: i32, by1: i32, bx2: i32, by2: i32, // 矩形 B
    ) -> i32 {
        use std::cmp::{max, min};

        // 两个矩形各自的面积（宽 × 高，宽、高必为非负）
        let area_a = (ax2 - ax1) * (ay2 - ay1);
        let area_b = (bx2 - bx1) * (by2 - by1);

        // 重叠部分的宽度和高度（若为负数则说明无重叠）
        let overlap_w = min(ax2, bx2) - max(ax1, bx1);
        let overlap_h = min(ay2, by2) - max(ay1, by1);

        // 实际重叠面积（仅当 overlap_w 和 overlap_h 均为正时才有正面积）
        let overlap_area = max(overlap_w, 0) * max(overlap_h, 0);

        // 总面积 = 两矩形面积之和 − 重叠面积
        area_a + area_b - overlap_area
    }
}
```
