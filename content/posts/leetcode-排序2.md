---
title: "leetcode-排序2"
date: 2026-10-05T00:34:59+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 合并区间

以数组 intervals 表示若干个区间的集合，其中单个区间为 intervals[i] = [starti, endi] 。请你合并所有重叠的区间，并返回 一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间 。


```
impl Solution {
    /// 合并所有重叠区间
    ///
    /// 思路：按左端点升序排序后，重叠区间必然相邻。
    /// 只需一次线性扫描，用结果数组的最后一个区间与当前区间比较即可。
    pub fn merge(mut intervals: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        // 按左端点升序排序，使重叠区间相邻
        intervals.sort_unstable_by_key(|p| p[0]);

        // 预分配容量，避免扩容；最坏情况下无重叠，容量正好够用
        let mut merged: Vec<Vec<i32>> = Vec::with_capacity(intervals.len());

        for interval in intervals {
            match merged.last_mut() {
                // 与上一个合并区间重叠：扩展右端点
                Some(last) if interval[0] <= last[1] => {
                    last[1] = last[1].max(interval[1]);
                }
                // 不重叠（或首个区间）：直接作为新区间加入
                _ => merged.push(interval),
            }
        }

        merged
    }
}
```
