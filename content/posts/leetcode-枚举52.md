---
title: "leetcode-枚举52"
date: 2026-07-09T10:05:01+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 移除栅栏得到的正方形田地的最大面积

有一个大型的 (m - 1) x (n - 1) 矩形田地，其两个对角分别是 (1, 1) 和 (m, n) ，田地内部有一些水平栅栏和垂直栅栏，分别由数组 hFences 和 vFences 给出。

水平栅栏为坐标 (hFences[i], 1) 到 (hFences[i], n)，垂直栅栏为坐标 (1, vFences[i]) 到 (m, vFences[i]) 。

返回通过 移除 一些栅栏（可能不移除）所能形成的最大面积的 正方形 田地的面积，或者如果无法形成正方形田地则返回 -1。

由于答案可能很大，所以请返回结果对 109 + 7 取余 后的值。

注意：田地外围两个水平栅栏（坐标 (1, 1) 到 (1, n) 和坐标 (m, 1) 到 (m, n) ）以及两个垂直栅栏（坐标 (1, 1) 到 (m, 1) 和坐标 (1, n) 到 (m, n) ）所包围。这些栅栏 不能 被移除。


```
use std::collections::HashSet;

impl Solution {
    pub fn maximize_square_area(m: i32, n: i32, h_fences: Vec<i32>, v_fences: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;

        // 计算所有可能的边长（任意两栅栏之间的距离）
        fn collect_edges(fences: &[i32], border: i32) -> HashSet<i32> {
            let mut points = Vec::with_capacity(fences.len() + 2);
            points.push(1);
            points.extend_from_slice(fences);
            points.push(border);
            points.sort_unstable();

            let mut edges = HashSet::with_capacity(points.len() * points.len() / 2);
            for i in 0..points.len() {
                for j in i + 1..points.len() {
                    edges.insert(points[j] - points[i]);
                }
            }
            edges
        };

        let h_edges = collect_edges(&h_fences, m);
        let v_edges = collect_edges(&v_fences, n);

        // 找到同时在水平和垂直方向上都存在的最大边长
        h_edges
            .intersection(&v_edges)
            .max()
            .map_or(-1, |&side| ((side as i64).pow(2) % MOD) as i32)
    }
}
```
