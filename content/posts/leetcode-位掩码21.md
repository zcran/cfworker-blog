---
title: "leetcode-位掩码21"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 完成任务的最少工作时间段


你被安排了 n 个任务。任务需要花费的时间用长度为 n 的整数数组 tasks 表示，第 i 个任务需要花费 tasks[i] 小时完成。一个 工作时间段 中，你可以 至多 连续工作 sessionTime 个小时，然后休息一会儿。

你需要按照如下条件完成给定任务：

· 如果你在某一个时间段开始一个任务，你需要在 同一个 时间段完成它。
· 完成一个任务后，你可以 立马 开始一个新的任务。
· 你可以按 任意顺序 完成任务。

给你 tasks 和 sessionTime ，请你按照上述要求，返回完成所有任务所需要的 最少 数目的 工作时间段 。

测试数据保证 sessionTime 大于等于 tasks[i] 中的 最大值 。


```
impl Solution {
    pub fn min_sessions(mut tasks: Vec<i32>, session_time: i32) -> i32 {
        // 降序排序，优先处理大任务，加速剪枝
        tasks.sort_unstable_by(|a, b| b.cmp(a));
        let mut ans = tasks.len();          // 初始上界：每个任务单独会话
        let mut sessions = Vec::new();      // 当前各会话已用时
        Self::dfs(&tasks, &mut ans, &mut sessions, session_time, 0);
        ans as i32
    }

    fn dfs(tasks: &[i32], ans: &mut usize, sessions: &mut Vec<i32>, limit: i32, idx: usize) {
        // 剪枝：当前会话数已不小于最优解
        if sessions.len() >= *ans {
            return;
        }
        if idx == tasks.len() {
            *ans = sessions.len();
            return;
        }

        let task = tasks[idx];
        let mut last = -1;  // 记录上一个尝试过的会话时长，用于去重

        // 尝试放入已有会话
        for i in 0..sessions.len() {
            let new_time = sessions[i] + task;
            if new_time <= limit && sessions[i] != last {
                last = sessions[i];
                sessions[i] = new_time;
                Self::dfs(tasks, ans, sessions, limit, idx + 1);
                sessions[i] -= task;
            }
        }

        // 新开一个会话
        sessions.push(task);
        Self::dfs(tasks, ans, sessions, limit, idx + 1);
        sessions.pop();
    }
}
```
