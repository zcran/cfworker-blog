---
title: "leetcode-堆49"
date: 2026-08-21T09:54:13+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 将区间分为最少组数

给你一个二维整数数组 intervals ，其中 intervals[i] = [leftˇi, rightˇi] 表示 闭 区间 [leftˇi, rightˇi] 。

你需要将 intervals 划分为一个或者多个区间 组 ，每个区间 只 属于一个组，且同一个组中任意两个区间 不相交 。

请你返回 最少 需要划分成多少个组。

如果两个区间覆盖的范围有重叠（即至少有一个公共数字），那么我们称这两个区间是 相交 的。比方说区间 [1, 5] 和 [5, 8] 相交。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    /// 最少分组数 = 同一时刻最多重叠的区间数
    ///
    /// 贪心：按左端点排序，最小堆维护各组右端点。
    /// 当前区间能放入最早结束的组（l > 堆顶）则复用，否则新建组。
    pub fn min_groups(intervals: Vec<Vec<i32>>) -> i32 {
        let mut intervals = intervals;
        intervals.sort_unstable_by(|a, b| a[0].cmp(&b[0]));

        // 最小堆，存储各组右端点
        let mut ends: BinaryHeap<Reverse<i32>> = BinaryHeap::new();

        for interval in intervals {
            let (l, r) = (interval[0], interval[1]);

            // 若最早结束的组已空出，复用它
            if let Some(&Reverse(earliest_end)) = ends.peek() {
                if earliest_end < l {
                    ends.pop(); // 复用该组
                }
            }
            ends.push(Reverse(r));
        }

        ends.len() as i32
    }
}
```
