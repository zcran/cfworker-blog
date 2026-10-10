---
title: "leetcode-数学41"
date: 2026-10-07T20:39:31+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 统计共同度过的日子数

Alice 和 Bob 计划分别去罗马开会。

给你四个字符串 arriveAlice ，leaveAlice ，arriveBob 和 leaveBob 。Alice 会在日期 arriveAlice 到 leaveAlice 之间在城市里（日期为闭区间），而 Bob 在日期 arriveBob 到 leaveBob 之间在城市里（日期为闭区间）。每个字符串都包含 5 个字符，格式为 "MM-DD" ，对应着一个日期的月和日。

请你返回 Alice和 Bob 同时在罗马的天数。

你可以假设所有日期都在 同一个 自然年，而且 不是 闰年。每个月份的天数分别为：[31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31] 。


```
impl Solution {
    pub fn count_days_together(
        arrive_alice: String,
        leave_alice: String,
        arrive_bob: String,
        leave_bob: String,
    ) -> i32 {
        const DAYS: [i32; 12] = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31];

        // 将 "MM-DD" 解析为该日期在一年中的第几天（1 ~ 365）
        let to_day = |s: &str| -> i32 {
            let b = s.as_bytes();
            let month = (b[0] - b'0') as i32 * 10 + (b[1] - b'0') as i32;
            let day = (b[3] - b'0') as i32 * 10 + (b[4] - b'0') as i32;
            DAYS[..month as usize - 1].iter().sum::<i32>() + day
        };

        let a_start = to_day(&arrive_alice);
        let a_end = to_day(&leave_alice);
        let b_start = to_day(&arrive_bob);
        let b_end = to_day(&leave_bob);

        // 两人区间交集长度：max(0, min(end) - max(start) + 1)
        let overlap_start = a_start.max(b_start);
        let overlap_end = a_end.min(b_end);
        (overlap_end - overlap_start + 1).max(0)
    }
}
```
