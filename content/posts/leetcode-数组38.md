---
title: "leetcode-数组38"
date: 2026-10-10T20:38:43+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 处理用时最长的那个任务的员工

共有 n 位员工，每位员工都有一个从 0 到 n - 1 的唯一 id 。

给你一个二维整数数组 logs ，其中 logs[i] = [idi, leaveTimei] ：

· idi 是处理第 i 个任务的员工的 id ，且
· leaveTimei 是员工完成第 i 个任务的时刻。所有 leaveTimei 的值都是 唯一 的。

注意，第 i 个任务在第 (i - 1) 个任务结束后立即开始，且第 0 个任务从时刻 0 开始。

返回处理用时最长的那个任务的员工的 id 。如果存在两个或多个员工同时满足，则返回几人中 最小 的 id 。


```
impl Solution {
    /// 返回处理用时最长的任务对应的员工 id；若并列，取最小 id。
    ///
    /// 第 0 个任务从时刻 0 开始，用时为 `logs[0][1]`；
    /// 第 i 个任务用时为 `logs[i][1] - logs[i-1][1]`。
    /// 遍历所有任务，维护最大用时及对应的最小员工 id。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn hardest_worker(n: i32, logs: Vec<Vec<i32>>) -> i32 {
        let _ = n; // 员工总数不影响结果
        let mut best_id = logs[0][0];
        let mut best_time = logs[0][1];

        for i in 1..logs.len() {
            let time = logs[i][1] - logs[i - 1][1];
            let id = logs[i][0];
            if time > best_time || (time == best_time && id < best_id) {
                best_time = time;
                best_id = id;
            }
        }

        best_id
    }
}
```
