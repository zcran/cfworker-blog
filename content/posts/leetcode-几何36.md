---
title: "leetcode-几何36"
date: 2026-05-11T20:20:56+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 最小矩形面积

二维平面上有 N 条直线，形式为 y = kx + b，其中 k、b为整数 且 k > 0。所有直线以 [k,b] 的形式存于二维数组 lines 中，不存在重合的两条直线。两两直线之间可能存在一个交点，最多会有 C²/N个交点。我们用一个平行于坐标轴的矩形覆盖所有的交点，请问这个矩形最小面积是多少。若直线之间无交点、仅有一个交点或所有交点均在同一条平行坐标轴的直线上，则返回0。

注意：返回结果是浮点数，与标准答案 绝对误差或相对误差 在 10^-4 以内的结果都被视为正确结果

```
use std::collections::HashMap;

impl Solution {
    /// 计算给定直线族所围成的最小轴对齐矩形面积。
    /// 输入 `lines` 为 `[[k, b], ...]`，表示直线 y = k*x + b。
    /// 返回矩形面积，若无法构成矩形则返回 0.0。
    pub fn min_rec_size(lines: Vec<Vec<i32>>) -> f64 {
        // 1. 按斜率分组，每组存储截距列表
        let mut groups: HashMap<i32, Vec<i32>> = HashMap::new();
        for line in lines {
            let k = line[0];
            let b = line[1];
            groups.entry(k).or_insert_with(Vec::new).push(b);
        }

        // 2. 对每组内的截距排序（用于取极值）
        for b_list in groups.values_mut() {
            b_list.sort_unstable();
        }

        // 3. 获取所有不同的斜率，并排序
        let mut slopes: Vec<i32> = groups.keys().copied().collect();
        slopes.sort_unstable();
        if slopes.len() <= 1 {
            return 0.0;
        }

        // 4. 辅助函数：计算相邻两组直线交点的 x 与 y 范围
        // 返回 (dxmin, dxmax, dymin, dymax) 四个边界值
        let compute = |k1: i32, k2: i32, b1: &[i32], b2: &[i32]| {
            let k1f = k1 as f64;
            let k2f = k2 as f64;
            let denom = (k1f - k2f) as f64;
            // 防止除零（斜率不同，denom != 0）
            let dx_min = -(b1[0] as f64 - b2[b2.len() - 1] as f64) / denom;
            let dx_max = -(b1[b1.len() - 1] as f64 - b2[0] as f64) / denom;
            let d1 = 1.0 / k1f;
            let d2 = 1.0 / k2f;
            let denom_y = d1 - d2;
            let dy_max = (b1[b1.len() - 1] as f64 * d1 - b2[0] as f64 * d2) / denom_y;
            let dy_min = (b1[0] as f64 * d1 - b2[b2.len() - 1] as f64 * d2) / denom_y;
            (dx_min, dx_max, dy_min, dy_max)
        };

        // 5. 遍历相邻斜率对，累积全局 x/y 极值
        let (x_min, x_max, y_min, y_max) = slopes
            .windows(2)
            .fold(
                (f64::INFINITY, f64::NEG_INFINITY, f64::INFINITY, f64::NEG_INFINITY),
                |(xmin, xmax, ymin, ymax), window| {
                    let k1 = window[0];
                    let k2 = window[1];
                    let b1 = &groups[&k1];
                    let b2 = &groups[&k2];
                    let (dxmin, dxmax, dymin, dymax) = compute(k1, k2, b1, b2);
                    (
                        xmin.min(dxmin),
                        xmax.max(dxmax),
                        ymin.min(dymin),
                        ymax.max(dymax),
                    )
                },
            );

        // 6. 若极值未更新（如只有一对组），则返回面积
        if x_min.is_infinite() || x_max.is_infinite() || y_min.is_infinite() || y_max.is_infinite() {
            0.0
        } else {
            (x_max - x_min) * (y_max - y_min)
        }
    }
}
```
