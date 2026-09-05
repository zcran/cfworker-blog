---
title: "leetcode-堆18"
date: 2026-08-21T09:54:11+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 最多可以参加的会议数目

给你一个数组 events，其中 events[i] = [startDayˇi, endDayˇi] ，表示会议 i 开始于 startDayˇi ，结束于 endDayˇi 。

你可以在满足 startDayˇi <= d <= endDayˇi 中的任意一天 d 参加会议 i 。在任意一天 d 中只能参加一场会议。

请你返回你可以参加的 最大 会议数目。


```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

impl Solution {
    /// 贪心：每天优先参加结束时间最早的会议
    pub fn max_events(mut events: Vec<Vec<i32>>) -> i32 {
        // 按开始时间升序排列
        events.sort_unstable_by_key(|e| e[0]);

        let mut heap = BinaryHeap::new(); // 小根堆，存储当前可参加会议的结束时间
        let mut day = 1;
        let mut i = 0; // 事件指针
        let mut ans = 0;

        while i < events.len() || !heap.is_empty() {
            // 将所有今天开始的会议加入堆
            while i < events.len() && events[i][0] <= day {
                heap.push(Reverse(events[i][1]));
                i += 1;
            }

            // 跳过已经结束的会议
            while let Some(&Reverse(end)) = heap.peek() {
                if end < day {
                    heap.pop();
                } else {
                    break;
                }
            }

            // 参加结束时间最早的会议
            if heap.pop().is_some() {
                ans += 1;
            }

            day += 1;
        }

        ans
    }
}
```
