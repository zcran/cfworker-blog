---
title: "leetcode-几何38"
date: 2026-05-11T20:20:56+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 交点

给定两条线段（表示为起点start = {X1, Y1}和终点end = {X2, Y2}），如果它们有交点，请计算其交点，没有交点则返回空值。

要求浮点型误差不超过10^-6。若有多个交点（线段重叠）则返回 X 值最小的点，X 坐标相同则返回 Y 值最小的点。

```
impl Solution {
    /// 求两条线段 AB 和 CD 的交点（若存在）。
    /// 线段端点分别为 start1, end1 和 start2, end2。
    /// 返回交点坐标 `[x, y]`，若无交点则返回空向量。
    pub fn intersection(
        start1: Vec<i32>,
        end1: Vec<i32>,
        start2: Vec<i32>,
        end2: Vec<i32>,
    ) -> Vec<f64> {
        use std::cmp::Ordering;

        // 将端点转换为元组，便于计算
        let a = (start1[0], start1[1]);
        let b = (end1[0], end1[1]);
        let c = (start2[0], start2[1]);
        let d = (end2[0], end2[1]);

        // 向量减法
        let sub = |p: (i32, i32), q: (i32, i32)| (p.0 - q.0, p.1 - q.1);
        // 二维叉积 (p × q)
        let cross = |p: (i32, i32), q: (i32, i32)| p.0 * q.1 - p.1 * q.0;
        // 检查参数 t = u / v 是否在 [0, 1] 范围内（u 和 v 同号且 |u| ≤ |v|）
        let in_range = |u: i32, v: i32| -> bool {
            if v == 0 { return false; }
            if u == 0 { return true; }
            (u > 0) == (v > 0) && u.abs() <= v.abs()
        };

        let ab = sub(b, a);
        let cd = sub(d, c);
        let ac = sub(c, a);

        let det = cross(ab, cd);
        let det_ac = cross(ac, cd);
        let det_ab = cross(ab, ac);   // 等价于 cross(ac, ab) 的相反数，但用于参数

        if det != 0 {
            // 不平行，唯一交点
            let t = det_ac as f64 / det as f64;   // 交点在 AB 上的参数
            let u = -det_ab as f64 / det as f64;  // 交点在 CD 上的参数
            if in_range(det_ac, det) && in_range(-det_ab, det) {
                let x = a.0 as f64 + t * ab.0 as f64;
                let y = a.1 as f64 + t * ab.1 as f64;
                vec![x, y]
            } else {
                vec![]
            }
        } else {
            // 平行或共线
            if cross(ac, cd) != 0 {
                // 平行但不共线，无交点
                return vec![];
            }
            // 共线，需要找重叠部分的端点（取最小坐标点）
            // 将四个端点投影到直线方向上，用标量参数表示位置
            // 选择任意非零方向轴（取 ab 或 cd，若为零向量则线段退化为点）
            let dir = if ab.0 != 0 || ab.1 != 0 { ab } else { cd };
            if dir.0 == 0 && dir.1 == 0 {
                // 两个点均退化，判断是否重合
                return if a == c { vec![a.0 as f64, a.1 as f64] } else { vec![] };
            }
            // 投影函数：点 p 在方向 dir 上的标量值（取点积）
            let project = |p: (i32, i32)| p.0 * dir.0 + p.1 * dir.1;
            let mut params = [project(a), project(b), project(c), project(d)];
            let points = [a, b, c, d];
            // 找到重叠区间：所有在另一线段上的点，取最小点
            // 简化：对所有点，检查是否在线段上（用投影参数判断）
            let eps = 1e-9;
            let on_segment = |p: (i32, i32), p1: (i32, i32), p2: (i32, i32)| -> bool {
                let min_x = p1.0.min(p2.0);
                let max_x = p1.0.max(p2.0);
                let min_y = p1.1.min(p2.1);
                let max_y = p1.1.max(p2.1);
                p.0 >= min_x && p.0 <= max_x && p.1 >= min_y && p.1 <= max_y
            };
            let mut candidates = Vec::new();
            // 检查每个端点是否在另一条线段上
            if on_segment(a, c, d) { candidates.push(a); }
            if on_segment(b, c, d) { candidates.push(b); }
            if on_segment(c, a, b) { candidates.push(c); }
            if on_segment(d, a, b) { candidates.push(d); }
            // 去重并找最小点（按 x 优先，再 y）
            candidates.sort_unstable();
            candidates.dedup();
            candidates
                .into_iter()
                .min_by(|&p, &q| (p.0, p.1).cmp(&(q.0, q.1)))
                .map(|p| vec![p.0 as f64, p.1 as f64])
                .unwrap_or_else(Vec::new)
        }
    }
}
```
