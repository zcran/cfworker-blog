---
title: "leetcode-数学89"
date: 2026-10-07T20:39:34+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 三角形的内角度数

给你一个长度为 3 的正整数数组 sides。

判断是否能够由 sides 中的三个元素作为边长，构成一个 面积为正 的三角形。

如果可以构成这样的三角形，返回一个包含 3 个浮点数的数组，表示该三角形的三个 内角（单位为 度），并按 非递减顺序 排序。否则，返回一个空数组。

与真实答案的误差在 10^-5 以内的结果都将被视为正确。


```
impl Solution {
    pub fn internal_angles(mut sides: Vec<i32>) -> Vec<f64> {
        // 排序后 a <= b <= c，小边对小角
        sides.sort_unstable();
        let (a, b, c) = (sides[0] as f64, sides[1] as f64, sides[2] as f64);

        // 三角形不等式：两边之和必须大于第三边（面积为正）
        if a + b <= c {
            return vec![];
        }

        // 余弦定理求角，结果转为角度制
        let to_deg = 180.0 / std::f64::consts::PI;
        let angle_a = ((b * b + c * c - a * a) / (2.0 * b * c)).acos() * to_deg;
        let angle_b = ((a * a + c * c - b * b) / (2.0 * a * c)).acos() * to_deg;

        // 第三个角用内角和 180° 减去前两个角，保证总和精确为 180°
        vec![angle_a, angle_b, 180.0 - angle_a - angle_b]
    }
}
```
