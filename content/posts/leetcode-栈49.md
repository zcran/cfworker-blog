---
title: "leetcode-栈49"
date: 2026-07-24T10:17:00+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 完成所有任务的最少时间

你有一台电脑，它可以 同时 运行无数个任务。给你一个二维整数数组 tasks ，其中 tasks[i] = [starti, endi, durationi] 表示第 i 个任务需要在 闭区间 时间段 [starti, endi] 内运行 durationi 个整数时间点（但不需要连续）。

当电脑需要运行任务时，你可以打开电脑，如果空闲时，你可以将电脑关闭。

请你返回完成所有任务的情况下，电脑最少需要运行多少秒。


```
impl Solution {
    /// 返回完成所有任务所需的最少运行秒数
    ///
    /// # 算法思路
    /// 贪心 + 区间调度（按结束时间排序，尽可能将任务安排在右侧）
    /// 1. 按结束时间升序排序所有任务
    /// 2. 用布尔数组 `running` 记录每个时间点是否已被占用
    /// 3. 对每个任务，优先利用已占用的时间点，不足的部分从右侧未占用时间点补充
    /// 4. 贪心选择右侧时间点是为了给后续任务留出更多空间
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n log n + n * D)，D 为任务平均区间长度，实际可优化
    /// - 空间复杂度：O(max_end_time)，用于记录运行状态
    pub fn find_minimum_time(tasks: Vec<Vec<i32>>) -> i32 {
        // 1. 按结束时间升序排序
        let mut tasks = tasks;
        tasks.sort_unstable_by(|a, b| a[1].cmp(&b[1]));

        // 2. 计算最大结束时间，初始化运行状态数组
        let max_end = tasks.last().unwrap()[1] as usize;
        let mut running = vec![false; max_end + 1];
        let mut total_time = 0;

        // 3. 贪心处理每个任务
        for task in &tasks {
            let start = task[0] as usize;
            let end = task[1] as usize;
            let mut remaining = task[2]; // 还需运行的时间点数

            // 统计当前任务区间内已被占用的时间点
            let occupied: i32 = running[start..=end].iter().map(|&b| b as i32).sum();
            remaining -= occupied;

            // 如果还需要额外时间，从右侧开始填补
            if remaining > 0 {
                total_time += remaining;

                // 从右向左选择未占用的时间点
                for time in (start..=end).rev() {
                    if remaining == 0 {
                        break;
                    }
                    if !running[time] {
                        running[time] = true;
                        remaining -= 1;
                    }
                }
            }
        }

        total_time
    }
}
```
