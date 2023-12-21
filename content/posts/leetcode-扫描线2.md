---
title: "Leetcode 扫描线2"
date: 2023-12-19T15:44:18+08:00
tags: ["leetcode", "扫描线"]
draft: false
---

## 矩形面积 II

给你一个轴对齐的二维数组 rectangles 。 对于 rectangle[i] = [x1, y1, x2, y2]，其中（x1，y1）是矩形 i 左下角的坐标， (x⌄i1, y⌄i1) 是该矩形 左下角 的坐标， (x⌄i2, y⌄i2) 是该矩形 右上角 的坐标。

计算平面中所有 rectangles 所覆盖的 总面积 。任何被两个或多个矩形覆盖的区域应只计算 一次 。

返回 总面积 。因为答案可能太大，返回 10^9 + 7 的 模 。

```

impl Solution {
    pub fn rectangle_area(rectangles: Vec<Vec<i32>>) -> i32 {
        let mut heights = vec![];
        for v in rectangles.iter() {
            heights.push(v[1]);
            heights.push(v[3]);
        }
        heights.sort();

        let mut rs = rectangles;
        rs.sort_by(|a, b| a[0].cmp(&b[0]));

        let mut res = 0;
        let mut height = 0;
        for h in heights.iter() {
            let a = (*h - height) as i64;
            let mut b = 0;
            let mut pre = 0;
            for r in rs.iter() {
                if r[1] <= height && r[3] >= *h {
                    b += (pre.max(r[2]) - pre.max(r[0])) as i64;
                    pre = pre.max(r[2]);
                }
            }
            height = *h;
            res += (a * b) % 1000000007;
        }
        (res % 1000000007) as i32
    }
}
```