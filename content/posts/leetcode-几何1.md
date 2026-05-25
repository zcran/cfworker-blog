---
title: "leetcode-几何1"
date: 2026-05-11T20:20:54+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 直线上最多的点数

给你一个数组 points ，其中 points[i] = [xi, yi] 表示 X-Y 平面上的一个点。求最多有多少个点在同一条直线上。

```
impl Solution {
    pub fn max_points(points: Vec<Vec<i32>>) -> i32 {
        use std::collections::HashMap;

        // 计算最大公约数（非负）
        fn gcd(mut a: i32, mut b: i32) -> i32 {
            while b != 0 {
                let t = b;
                b = a % b;
                a = t;
            }
            a.abs() // 保证返回非负
        }

        /// 将两点确定的直线归一化为唯一的三元组 (A, B, C)
        /// 直线方程：A*x + B*y + C = 0，并满足：
        /// - 第一个非零系数为正
        /// - 系数互质（除以最大公约数）
        fn normalize_line(x1: i32, y1: i32, x2: i32, y2: i32) -> (i32, i32, i32) {
            let a = y2 - y1;
            let b = x1 - x2;
            let c = x2 * y1 - x1 * y2;

            // 找到第一个非零系数，用于确定符号
            let first_non_zero = if a != 0 { a } else if b != 0 { b } else { c };
            // 三个系数的最大公约数（忽略符号）
            let g = gcd(first_non_zero, gcd(a, b));
            debug_assert!(g != 0, "两点不同，不会全零");
            // 确保第一个非零系数为正
            let sign = if first_non_zero < 0 { -1 } else { 1 };
            (a / (g * sign), b / (g * sign), c / (g * sign))
        }

        // 将 Vec<Vec<i32>> 转换为 Vec<(i32,i32)> 方便使用
        let pts: Vec<(i32, i32)> = points
            .into_iter()
            .map(|p| (p[0], p[1]))
            .collect();
        let n = pts.len();
        if n < 2 {
            return n as i32;
        }

        // 对每个点作为基准，计算它与其他点构成的直线，统计共线点数量
        let max_on_line = (0..n)
            .map(|i| {
                let (x1, y1) = pts[i];
                // 从 i+1 开始，统计每个直线出现的次数
                (i + 1..n)
                    .map(|j| {
                        let (x2, y2) = pts[j];
                        normalize_line(x1, y1, x2, y2)
                    })
                    .fold(HashMap::new(), |mut counts, line| {
                        *counts.entry(line).or_insert(0) += 1;
                        counts
                    })
                    .values()
                    .copied()
                    .max()
                    .unwrap_or(0) // 无点则 0
            })
            .max()
            .unwrap_or(0);

        // 加上基准点自身
        max_on_line + 1
    }
}
```
