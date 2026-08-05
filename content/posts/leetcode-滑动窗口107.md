---
title: "leetcode-滑动窗口107"
date: 2026-07-18T11:02:35+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 重新安排会议得到最多空余时间 I

给你一个整数 eventTime 表示一个活动的总时长，这个活动开始于 t = 0 ，结束于 t = eventTime 。

同时给你两个长度为 n 的整数数组 startTime 和 endTime 。它们表示这次活动中 n 个时间 没有重叠 的会议，其中第 i 个会议的时间为 [startTime[i], endTime[i]] 。

你可以重新安排 至多 k 个会议，安排的规则是将会议时间平移，且保持原来的 会议时长 ，你的目的是移动会议后 最大化 相邻两个会议之间的 最长 连续空余时间。

移动前后所有会议之间的 相对 顺序需要保持不变，而且会议时间也需要保持互不重叠。

请你返回重新安排会议以后，可以得到的 最大 空余时间。

注意，会议 不能 安排到整个活动的时间以外。


```
impl Solution {
    pub fn max_free_time(event_time: i32, k: i32, start_time: Vec<i32>, end_time: Vec<i32>) -> i32 {
        let n = start_time.len();
        let k = k as usize;

        // 前缀和：快速计算任意连续 k 个会议的总时长
        let mut prefix = vec![0; n + 1];
        for i in 0..n {
            prefix[i + 1] = prefix[i] + (end_time[i] - start_time[i]);
        }

        let mut max_gap = 0;

        // 枚举长度为 k 的会议块，计算移除它们后能获得的最大空档
        for block_end in k - 1..n {
            let block_start = block_end - k + 1;

            // 空档左边界：若块从第一个会议开始，则为 0；否则为前一个会议的结束时间
            let left_boundary = if block_start == 0 { 0 } else { end_time[block_start - 1] };

            // 空档右边界：若块到最后一个会议结束，则为 event_time；否则为后一个会议的开始时间
            let right_boundary = if block_end == n - 1 { event_time } else { start_time[block_end + 1] };

            // 块内会议总时长
            let block_duration = prefix[block_end + 1] - prefix[block_start];

            // 空档长度 = 边界距离 - 块内会议占用的时间
            let gap = right_boundary - left_boundary - block_duration;
            max_gap = max_gap.max(gap);
        }

        max_gap
    }
}
```
