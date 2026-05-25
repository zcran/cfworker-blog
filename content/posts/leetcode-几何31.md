---
title: "leetcode-几何31"
date: 2026-05-11T20:20:56+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 找到最大三角形面积

给你一个二维数组 coords，大小为 n x 2，表示一个无限笛卡尔平面上 n 个点的坐标。

找出一个 最大 三角形的 两倍 面积，其中三角形的三个顶点来自 coords 中的任意三个点，并且该三角形至少有一条边与 x 轴或 y 轴平行。严格地说，如果该三角形的最大面积为 A，则返回 2 * A。

如果不存在这样的三角形，返回 -1。

注意，三角形的面积 不能 为零。


```
use std::collections::HashMap;

impl Solution {
    /// 计算由两个点（边平行于坐标轴）所能构成的最大矩形面积。
    /// 若不存在正面积（点数不足或所有点共线等），返回 -1。
    pub fn max_area(coords: Vec<Vec<i32>>) -> i64 {
        // 辅助函数：根据投影函数 (x, y) 计算当前方向下的最大面积
        fn calc<F>(coords: &[Vec<i32>], mut project: F) -> i64
        where
            F: FnMut(&Vec<i32>) -> (i32, i32),
        {
            let mut min_x = i32::MAX;
            let mut max_x = i32::MIN;
            // 每个 x 坐标对应的 (最小 y, 最大 y)
            let mut y_range: HashMap<i32, (i32, i32)> = HashMap::new();

            for point in coords {
                let (x, y) = project(point);
                min_x = min_x.min(x);
                max_x = max_x.max(x);
                let entry = y_range.entry(x).or_insert((y, y));
                entry.0 = entry.0.min(y);
                entry.1 = entry.1.max(y);
            }

            // 遍历每个 x，计算以该 x 所在竖直线为一边的矩形面积
            y_range
                .iter()
                .map(|(&x, &(min_y, max_y))| {
                    let height = (max_y - min_y) as i64;
                    let width = (max_x - x).max(x - min_x) as i64;
                    height * width
                })
                .max()
                .unwrap_or(0)
        }

        // 原始方向（以 x 为横坐标）
        let area1 = calc(&coords, |p| (p[0], p[1]));
        // 交换坐标方向（相当于旋转 90 度，以 y 为横坐标）
        let area2 = calc(&coords, |p| (p[1], p[0]));
        let best = area1.max(area2);
        if best > 0 { best } else { -1 }
    }
}
```
