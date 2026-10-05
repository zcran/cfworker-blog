---
title: "leetcode-贪心22"
date: 2026-10-03T12:55:35+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 设置交集大小至少为2

给你一个二维整数数组 intervals ，其中 intervals[i] = [starti, endi] 表示从 starti 到 endi 的所有整数，包括 starti 和 endi 。

包含集合 是一个名为 nums 的数组，并满足 intervals 中的每个区间都 至少 有 两个 整数在 nums 中。

例如，如果 intervals = [[1,3], [3,7], [8,9]] ，那么 [1,2,4,7,8,9] 和 [2,3,4,8,9] 都符合 包含集合 的定义。

返回包含集合可能的最小大小。


```
impl Solution {
    pub fn intersection_size_two(mut intervals: Vec<Vec<i32>>) -> i32 {
        // 按右端点升序排序，贪心地在每个区间右端附近取点
        intervals.sort_unstable_by_key(|v| v[1]);

        // picked1 / picked2: 已选取的两个最大点（picked2 为最近选的点，picked1 次之）
        // cnt: 已选点总数
        let (mut cnt, mut picked1, mut picked2) = (0, i32::MIN, i32::MIN);

        for itv in &intervals {
            let (start, end) = (itv[0], itv[1]);
            if start > picked2 {
                // 当前区间不含任何已选点：需选两个新点，尽量靠近右端
                cnt += 2;
                picked1 = end - 1;
                picked2 = end;
            } else if start > picked1 {
                // 当前区间只含 picked2 一个点：再补选一个点
                cnt += 1;
                picked1 = if picked2 == end { end - 1 } else { picked2 };
                picked2 = end;
            }
            // 否则区间已含两个点，无需操作
        }

        cnt
    }
}
```
