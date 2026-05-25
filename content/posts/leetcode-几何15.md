---
title: "leetcode-几何15"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 最接近原点的 K 个点

给定一个数组 points ，其中 points[i] = [xi, yi] 表示 X-Y 平面上的一个点，并且是一个整数 k ，返回离原点 (0,0) 最近的 k 个点。

这里，平面上两点之间的距离是 欧几里德距离（ √(x1 - x2)2 + (y1 - y2)2 ）。

你可以按 任何顺序 返回答案。除了点坐标的顺序之外，答案 确保 是 唯一 的。

```
impl Solution {
    pub fn k_closest(points: Vec<Vec<i32>>, k: i32) -> Vec<Vec<i32>> {
        let k = k as usize;
        if k >= points.len() {
            return points;
        }
        let mut points = points;
        // 部分排序 + 截断（仍然是命令式操作，但可封装为函数式风格）
        points.select_nth_unstable_by_key(k, |p| {
            let x = p[0] as i64;
            let y = p[1] as i64;
            x * x + y * y
        });
        points.truncate(k);
        points
    }
}
```
