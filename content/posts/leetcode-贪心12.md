---
title: "leetcode-贪心12"
date: 2026-10-03T12:55:34+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 无重叠区间

给定一个区间的集合 intervals ，其中 intervals[i] = [starti, endi] 。返回 需要移除区间的最小数量，使剩余区间互不重叠 。

注意 只在一点上接触的区间是 不重叠的。例如 [1, 2] 和 [2, 3] 是不重叠的。


```
impl Solution {
    pub fn erase_overlap_intervals(mut intervals: Vec<Vec<i32>>) -> i32 {
        // 贪心：按右端点升序排序，优先保留右端点小、更"早结束"的区间
        intervals.sort_unstable_by_key(|a| a[1]);

        // kept: 最多能保留的不重叠区间数
        // end:  上一个保留区间的右端点
        let (mut kept, mut end) = (0, i32::MIN);

        for p in &intervals {
            // 当前区间左端点 >= 上一个保留区间的右端点时，可以保留（端点接触算不重叠）
            if p[0] >= end {
                kept += 1;
                end = p[1];
            }
        }

        // 需要移除的数量 = 总数 - 最多保留数
        intervals.len() as i32 - kept
    }
}
```
