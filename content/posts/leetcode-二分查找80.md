---
title: "leetcode-二分查找80"
date: 2026-09-28T12:29:15+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 分割正方形 I

给你一个二维整数数组 squares ，其中 squares[i] = [xi, yi, li] 表示一个与 x 轴平行的正方形的左下角坐标和正方形的边长。

找到一个最小的 y 坐标，它对应一条水平线，该线需要满足它以上正方形的总面积 等于 该线以下正方形的总面积。

答案如果与实际答案的误差在 10-5 以内，将视为正确答案。

注意：正方形 可能会 重叠。重叠区域应该被 多次计数 。


```
impl Solution {
    pub fn separate_squares(squares: Vec<Vec<i32>>) -> f64 {
        let mut total_area = 0.0_f64;
        let mut max_y = 0.0_f64;
        for sq in &squares {
            let l = sq[2] as f64;
            total_area += l * l;
            max_y = max_y.max((sq[1] + sq[2]) as f64);
        }
        let half = total_area / 2.0;

        // 计算水平线 y = limit_y 以下的总面积
        let area_below = |limit_y: f64| -> f64 {
            let mut area = 0.0;
            for sq in &squares {
                let y = sq[1] as f64;
                if y < limit_y {
                    let l = sq[2] as f64;
                    area += l * (limit_y - y).min(l);
                }
            }
            area
        };

        // 二分查找使下半面积 >= 总面积一半的最小 y
        let mut lo = 0.0;
        let mut hi = max_y;
        while hi - lo > 1e-5 {
            let mid = (lo + hi) / 2.0;
            if area_below(mid) >= half {
                hi = mid;
            } else {
                lo = mid;
            }
        }
        hi
    }
}
```
