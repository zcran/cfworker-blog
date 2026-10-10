---
title: "leetcode-数组53"
date: 2026-10-10T20:38:44+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 按下时间最长的按钮

给你一个二维数组 events，表示孩子在键盘上按下一系列按钮触发的按钮事件。

每个 events[i] = [indexi, timei] 表示在时间 timei 时，按下了下标为 indexi 的按钮。

数组按照 time 的递增顺序排序。
按下一个按钮所需的时间是连续两次按钮按下的时间差。按下第一个按钮所需的时间就是其时间戳。

返回按下时间 最长 的按钮的 index。如果有多个按钮的按下时间相同，则返回 index 最小的按钮。


```
impl Solution {
    /// 返回按下时间最长的按钮下标；若并列，返回下标最小的。
    ///
    /// 第 0 个按钮的按下时间为其时间戳 `events[0][1]`；
    /// 第 i 个按钮的按下时间为 `events[i][1] - events[i-1][1]`。
    /// 遍历所有事件，维护最大按下时间及对应的最小按钮下标。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn button_with_longest_time(events: Vec<Vec<i32>>) -> i32 {
        let mut best_idx = events[0][0];
        let mut best_time = events[0][1];

        for i in 1..events.len() {
            let time = events[i][1] - events[i - 1][1];
            let idx = events[i][0];
            if time > best_time || (time == best_time && idx < best_idx) {
                best_time = time;
                best_idx = idx;
            }
        }

        best_idx
    }
}
```
