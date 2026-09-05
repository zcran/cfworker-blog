---
title: "leetcode-堆38"
date: 2026-08-21T09:54:13+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 两个最好的不重叠活动

给你一个下标从 0 开始的二维整数数组 events ，其中 events[i] = [startTimeˇi, endTimeˇi, valueˇi] 。第 i 个活动开始于 startTimeˇi ，结束于 endTimeˇi ，如果你参加这个活动，那么你可以得到价值 valueˇi 。你 最多 可以参加 两个时间不重叠 活动，使得它们的价值之和 最大 。

请你返回价值之和的 最大值 。

注意，活动的开始时间和结束时间是 包括 在活动时间内的，也就是说，你不能参加两个活动且它们之一的开始时间等于另一个活动的结束时间。更具体的，如果你参加一个活动，且结束时间为 t ，那么下一个活动必须在 t + 1 或之后的时间开始。


```
impl Solution {
    pub fn max_two_events(events: Vec<Vec<i32>>) -> i32 {
        // (time, kind, value): kind=0=start, kind=1=end
        // Sorting: start events come before end events at same time,
        // ensuring a starting event cannot pair with an ending event at the same timestamp.
        let mut timeline: Vec<(i32, i32, i32)> = Vec::with_capacity(events.len() * 2);
        for e in events {
            timeline.push((e[0], 0, e[2])); // start
            timeline.push((e[1], 1, e[2])); // end
        }
        timeline.sort_unstable();

        let mut ans = 0;
        let mut best_single = 0;

        for (_, kind, val) in timeline {
            if kind == 0 {
                // Start event: try pairing with the best finished event so far
                ans = ans.max(val + best_single);
            } else {
                // End event: update the best single event value
                best_single = best_single.max(val);
            }
        }

        ans
    }
}
```
