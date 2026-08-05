---
title: "leetcode-计数6"
date: 2026-08-01T10:40:51+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 任务调度器

给你一个用字符数组 tasks 表示的 CPU 需要执行的任务列表，用字母 A 到 Z 表示，以及一个冷却时间 n。每个周期或时间间隔允许完成一项任务。任务可以按任何顺序完成，但有一个限制：两个 相同种类 的任务之间必须有长度为 n 的冷却时间。

返回完成所有任务所需要的 最短时间间隔 。


```
impl Solution {
    /// 计算完成所有任务所需的最短时间间隔
    ///
    /// # 算法思路
    /// 出现次数最多的任务决定了所需的最少时间。
    /// 设最高频率为 `max_freq`，有 `max_count` 个任务达到该频率。
    ///
    /// 将任务排列成框架：
    /// ```
    /// [A][_][_][A][_][_][A]   (n = 2)
    /// ```
    /// - 前 `max_freq - 1` 行每行需要 `n + 1` 个时间单位
    /// - 最后一行有 `max_count` 个任务
    /// - 总时间 = `(max_freq - 1) * (n + 1) + max_count`
    ///
    /// 如果任务总数超过这个框架大小，则无需空闲时间，直接返回任务总数。
    ///
    /// # 复杂度
    /// - 时间：O(T)，T 为任务数量
    /// - 空间：O(1)
    pub fn least_interval(tasks: Vec<char>, n: i32) -> i32 {
        // 统计每个任务的出现次数（26 个大写字母）
        let mut freq = [0; 26];
        let mut max_freq = 0;      // 最高出现频率
        let mut max_count = 0;     // 达到最高频率的任务种类数

        for &task in &tasks {
            let idx = (task as u8 - b'A') as usize;
            freq[idx] += 1;

            // 更新最高频率和计数
            if freq[idx] > max_freq {
                max_freq = freq[idx];
                max_count = 1;
            } else if freq[idx] == max_freq {
                max_count += 1;
            }
        }

        // 计算理论最短时间
        // 框架大小 = (max_freq - 1) * (n + 1) + max_count
        let intervals = (max_freq - 1) * (n + 1) + max_count;

        // 如果任务总数比框架大，说明可以不需空闲时间连续执行
        tasks.len().max(intervals as usize) as i32
    }
}
```
