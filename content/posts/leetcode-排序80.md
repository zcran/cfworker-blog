---
title: "leetcode-排序80"
date: 2026-10-05T00:35:05+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 维持亮度的最小总能量

给你一个整数 n，表示有 n 个灯泡排成一排，下标从 0 到 n - 1。

同时给你一个整数 brightness 和一个二维整数数组 intervals，其中 intervals[i] = [starti, endi] 表示一个 闭 时间区间，在该时间区间内 必须 满足照明要求。

在每个时间单位，每个灯泡都可以独立地开启或关闭。开启的灯泡会 照亮 其自身的位置及其 相邻 的位置（如果存在）。

某个单位时间的 总照明度 是被 照亮 的位置数量。每个位置 至多 只计算 一次。

对于一个单位时间，如果它被 intervals 中 至少 一个时间区间覆盖，那么这个单位时间内 总照明度 必须 至少 为 brightness。如果一个单位时间没有被任何时间区间覆盖，那么所有灯泡可以保持关闭。一个单位时间内开启的一个灯泡消耗 1 单位的能量。

返回一个整数，表示所需的 最小 总能量。


```
impl Solution {
    pub fn min_energy(_n: i32, brightness: i32, mut intervals: Vec<Vec<i32>>) -> i64 {
        // 按左端点升序排序
        intervals.sort_unstable_by_key(|p| p[0]);

        // 合并重叠区间，累计覆盖的总时间长度
        let mut total_len: i64 = 0;
        let (mut left, mut right) = (0, -1);
        for p in &intervals {
            if p[0] <= right {
                // 与当前合并区间重叠，扩展右端点
                right = right.max(p[1]);
            } else {
                // 与当前区间不相交，结算前一个合并区间
                total_len += (right - left + 1) as i64;
                left = p[0];
                right = p[1];
            }
        }
        total_len += (right - left + 1) as i64;

        // 每个灯泡最多照亮自身及相邻共 3 个位置，
        // 因此达到 brightness 至少需开启 (brightness + 2) / 3 个灯泡
        let bulbs = (brightness + 2) / 3;

        bulbs as i64 * total_len
    }
}
```
