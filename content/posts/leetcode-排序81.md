---
title: "leetcode-排序81"
date: 2026-10-05T00:35:05+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 筛选忙碌区间

给你一个二维整数数组 occupiedIntervals，其中 occupiedIntervals[i] = [starti, endi] 表示你处于忙碌状态的一个时间区间。每个区间从 starti 开始，到 endi 结束，并且 包含 两个端点。这些区间可能会 重叠。

此外，另给你两个整数 freeStart 和 freeEnd，它们定义了一个你空闲的时间区间。该空闲区间从 freeStart 开始，到 freeEnd 结束，并且 包含 两个端点。

你的任务是先将所有重叠或相接的忙碌区间 合并 ，然后从合并后的忙碌区间中 移除 空闲区间内的 所有 整数点。

如果第二个区间正好从第一个区间结束后的下一个位置开始，则称这两个区间相接。例如，[1, 1] 和 [2, 2] 相接，应合并为 [1, 2]。

返回按 升序 排列的 剩余 忙碌区间。返回的区间必须 互不重叠 ，并且区间数量应尽可能 最少 。如果没有剩余的忙碌整数点，则返回 空列表 。




```
impl Solution {
    pub fn filter_occupied_intervals(
        mut occupied_intervals: Vec<Vec<i32>>,
        free_start: i32,
        free_end: i32,
    ) -> Vec<Vec<i32>> {
        // 按左端点升序排序
        occupied_intervals.sort_unstable_by_key(|a| a[0]);

        let mut result: Vec<Vec<i32>> = Vec::new();

        // 将合并后的忙碌区间 [l, r] 加入结果，同时挖去空闲区间 [free_start, free_end]
        let mut add = |l: i32, r: i32, result: &mut Vec<Vec<i32>>| {
            if r < free_start || l > free_end {
                // 与空闲区间不相交，整体保留
                result.push(vec![l, r]);
            } else {
                // 保留空闲区间左侧的剩余部分
                if l < free_start {
                    result.push(vec![l, free_start - 1]);
                }
                // 保留空闲区间右侧的剩余部分
                if r > free_end {
                    result.push(vec![free_end + 1, r]);
                }
            }
        };

        // 合并忙碌区间（相接的区间也合并）
        let (mut left, mut max_r) = (occupied_intervals[0][0], occupied_intervals[0][1]);
        for p in &occupied_intervals[1..] {
            let (l, r) = (p[0], p[1]);
            if l - 1 > max_r {
                // 出现断层，结算前一个合并区间
                add(left, max_r, &mut result);
                left = l;
            }
            max_r = max_r.max(r);
        }
        add(left, max_r, &mut result);

        result
    }
}
```
