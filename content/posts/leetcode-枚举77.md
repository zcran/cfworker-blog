---
title: "leetcode-枚举77"
date: 2026-07-09T10:05:03+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 重新安排会议得到最多空余时间 II

给你一个整数 eventTime 表示一个活动的总时长，这个活动开始于 t = 0 ，结束于 t = eventTime 。

同时给你两个长度为 n 的整数数组 startTime 和 endTime 。它们表示这次活动中 n 个时间 没有重叠 的会议，其中第 i 个会议的时间为 [startTime[i], endTime[i]] 。

你可以重新安排 至多 一个会议，安排的规则是将会议时间平移，且保持原来的 会议时长 ，你的目的是移动会议后 最大化 最长 连续空余时间。

请你返回重新安排会议以后，可以得到的 最大 空余时间。

注意，会议 不能 安排到整个活动的时间以外，且会议之间需要保持互不重叠。

注意：重新安排会议以后，会议之间的顺序可以发生改变。


```
impl Solution {
    pub fn max_free_time(event_time: i32, start_time: Vec<i32>, end_time: Vec<i32>) -> i32 {
        let n = start_time.len();

        // q[i] 标记会议 i 是否可以被移动（即会议持续时间 <= 其左右两侧空闲时间的最大值）
        let mut can_move = vec![false; n];

        // 从左到右扫描：记录会议左侧能够提供的最大空闲时间
        let mut max_left_gap = 0;
        // 从右到左扫描：记录会议右侧能够提供的最大空闲时间
        let mut max_right_gap = 0;

        for i in 0..n {
            let j = n - i - 1; // 对称的右侧索引

            // === 从左到右处理会议 i ===
            let current_duration = end_time[i] - start_time[i];
            // 如果会议持续时间 <= 左侧最大空闲时间，则该会议可以移动到左侧空闲段
            if current_duration <= max_left_gap {
                can_move[i] = true;
            }
            // 更新左侧最大空闲时间：当前会议左侧的空闲时间
            let left_gap = if i == 0 {
                start_time[i] // 第一个会议左侧是从 0 开始的空闲时间
            } else {
                start_time[i] - end_time[i - 1] // 会议 i-1 与会议 i 之间的空闲
            };
            max_left_gap = max_left_gap.max(left_gap);

            // === 从右到左处理会议 j ===
            let current_duration_j = end_time[j] - start_time[j];
            // 如果会议持续时间 <= 右侧最大空闲时间，则该会议可以移动到右侧空闲段
            if current_duration_j <= max_right_gap {
                can_move[j] = true;
            }
            // 更新右侧最大空闲时间：当前会议右侧的空闲时间
            let right_gap = if i == 0 {
                event_time - end_time[j] // 最后一个会议右侧到活动结束的空闲
            } else {
                start_time[n - i] - end_time[j] // 会议 j 与下一个会议之间的空闲
            };
            max_right_gap = max_right_gap.max(right_gap);
        }

        // 计算移动会议后可以获得的最大空闲时间
        let mut answer = 0;
        for i in 0..n {
            // 计算会议 i 左右两侧可用的总空闲时间（包括会议本身占用的时间）
            let left_boundary = if i == 0 { 0 } else { end_time[i - 1] };
            let right_boundary = if i == n - 1 { event_time } else { start_time[i + 1] };
            let total_free_around = right_boundary - left_boundary;
            let meeting_duration = end_time[i] - start_time[i];

            if can_move[i] {
                // 如果会议可以被移动，可以将会议移走，获得其左右两侧合并的空闲时间
                answer = answer.max(total_free_around);
            } else {
                // 如果会议不能被移动，则只能获得排除会议自身占用的空闲时间
                answer = answer.max(total_free_around - meeting_duration);
            }
        }

        answer
    }
}
```
