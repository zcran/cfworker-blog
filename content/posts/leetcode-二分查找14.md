---
title: "leetcode-二分查找14"
date: 2026-09-28T12:29:10+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 寻找右区间

给你一个区间数组 intervals ，其中 intervals[i] = [starti, endi] ，且每个 starti 都 不同 。

区间 i 的 右侧区间 是满足 startj >= endi，且 startj 最小 的区间 j。注意 i 可能等于 j 。

返回一个由每个区间 i 对应的 右侧区间 下标组成的数组。如果某个区间 i 不存在对应的 右侧区间 ，则下标 i 处的值设为 -1 。


```
impl Solution {
    pub fn find_right_interval(intervals: Vec<Vec<i32>>) -> Vec<i32> {
        let n = intervals.len();

        // 记录每个起始位置对应的原始下标
        let mut starts: Vec<(i32, usize)> = intervals
            .iter()
            .enumerate()
            .map(|(i, e)| (e[0], i))
            .collect();
        starts.sort_unstable();

        // 对每个区间，二分查找第一个 start >= end 的区间
        (0..n)
            .map(|i| {
                let end = intervals[i][1];
                let pos = starts.partition_point(|&(s, _)| s < end);
                if pos < n { starts[pos].1 as i32 } else { -1 }
            })
            .collect()
    }
}
```
