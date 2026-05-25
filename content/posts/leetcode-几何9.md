---
title: "leetcode-几何9"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 矩形重叠

矩形以列表 [x1, y1, x2, y2] 的形式表示，其中 (x1, y1) 为左下角的坐标，(x2, y2) 是右上角的坐标。矩形的上下边平行于 x 轴，左右边平行于 y 轴。

如果相交的面积为 正 ，则称两矩形重叠。需要明确的是，只在角或边接触的两个矩形不构成重叠。

给出两个矩形 rec1 和 rec2 。如果它们重叠，返回 true；否则，返回 false 。

```
impl Solution {
    pub fn is_rectangle_overlap(rec1: Vec<i32>, rec2: Vec<i32>) -> bool {
        // 定义：两个区间 [a1, a2] 和 [b1, b2] 是否重叠（区间长度 > 0）
        let overlap = |a1: i32, a2: i32, b1: i32, b2: i32| -> bool {
            a1 < b2 && b1 < a2 // 重叠的条件：左边界小于右边界
        };

        let (x1, y1, x2, y2) = (rec1[0], rec1[1], rec1[2], rec1[3]);
        let (x3, y3, x4, y4) = (rec2[0], rec2[1], rec2[2], rec2[3]);

        overlap(x1, x2, x3, x4) && overlap(y1, y2, y3, y4)
    }
}
```
