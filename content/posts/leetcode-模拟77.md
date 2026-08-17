---
title: "leetcode-模拟77"
date: 2026-08-08T11:31:13+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 会议室 III

给你一个整数 n ，共有编号从 0 到 n - 1 的 n 个会议室。

给你一个二维整数数组 meetings ，其中 meetings[i] = [starti, endi] 表示一场会议将会在 半闭 时间区间 [starti, endi) 举办。所有 starti 的值 互不相同 。

会议将会按以下方式分配给会议室：

1. 每场会议都会在未占用且编号 最小 的会议室举办。
2. 如果没有可用的会议室，会议将会延期，直到存在空闲的会议室。延期会议的持续时间和原会议持续时间 相同 。
3. 当会议室处于未占用状态时，将会优先提供给原 开始 时间更早的会议。

返回举办最多次会议的房间 编号 。如果存在多个房间满足此条件，则返回编号 最小 的房间。

半闭区间 [a, b) 是 a 和 b 之间的区间，包括 a 但 不包括 b 。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    /// 双堆模拟会议室分配：
    /// - idle: 空闲房间最小堆（按房间号）
    /// - busy: 使用中房间最小堆（按结束时间，相同时按房间号）
    ///
    /// 每场会议按开始时间顺序处理：
    /// 1. 先释放所有已结束的房间到 idle
    /// 2. 如有空闲房间，取编号最小的；否则取最早结束的房间（会议延期）
    /// 3. 将占用的房间重新放入 busy，并统计使用次数
    pub fn most_booked(n: i32, mut meetings: Vec<Vec<i32>>) -> i32 {
        meetings.sort_unstable_by_key(|m| m[0]);

        let n = n as usize;
        let mut busy: BinaryHeap<Reverse<(i64, usize)>> = BinaryHeap::new(); // (end_time, room)
        let mut idle: BinaryHeap<Reverse<usize>> = (0..n).map(Reverse).collect(); // available rooms

        let mut count = vec![0; n];

        for m in &meetings {
            let start = m[0] as i64;
            let end = m[1] as i64;

            // 释放所有在当前会议开始前已结束的房间
            while let Some(&Reverse((t, room))) = busy.peek() {
                if t <= start {
                    idle.push(Reverse(room));
                    busy.pop();
                } else {
                    break;
                }
            }

            let room = if let Some(Reverse(room)) = idle.pop() {
                // 有空闲房间，直接分配
                busy.push(Reverse((end, room)));
                room
            } else {
                // 无空闲房间，取最早结束的房间，会议延期
                let Reverse((t, room)) = busy.pop().unwrap();
                busy.push(Reverse((t + end - start, room)));
                room
            };

            count[room] += 1;
        }

        // 找使用次数最多的房间（次数相同时取编号最小）
        let mut ans = 0;
        for i in 1..n {
            if count[i] > count[ans] {
                ans = i;
            }
        }

        ans as i32
    }
}
```
