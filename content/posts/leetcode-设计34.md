---
title: "leetcode-设计34"
date: 2026-07-14T10:41:26+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 设计考试分数记录器

Alice 经常参加考试，并希望跟踪她的分数以及计算特定时间段内的总分数。

请实现 ExamTracker 类：

· ExamTracker(): 初始化 ExamTracker 对象。
· void record(int time, int score): Alice 在时间 time 参加了一次新考试，获得了分数 score。
· long long totalScore(int startTime, int endTime): 返回一个整数，表示 Alice 在 startTime 和 endTime（两者都包含）之间参加的所有考试的 总 分数。如果在指定时间间隔内 Alice 没有参加任何考试，则返回 0。

保证函数调用是按时间顺序进行的。即，

· 对 record() 的调用将按照 严格递增 的 time 进行。
· Alice 永远不会查询需要未来信息的总分数。也就是说，如果最近一次 record() 调用中的 time = t，那么 totalScore() 总是满足 startTime <= endTime <= t 。


```
/// 考试分数追踪器
///
/// 记录每次考试的时间和分数，支持时间范围总分查询
/// 时间复杂度: record O(1), totalScore O(log n)
/// 空间复杂度: O(n)
struct ExamTracker {
    times: Vec<i32>,           // 考试时间（严格递增）
    prefix: Vec<i64>,          // 前缀和，prefix[i] = 前 i 次考试的总分
}

impl ExamTracker {
    /// 初始化考试追踪器
    pub fn new() -> Self {
        ExamTracker {
            times: Vec::new(),
            prefix: vec![0],
        }
    }

    /// 记录一次考试
    pub fn record(&mut self, time: i32, score: i32) {
        self.times.push(time);
        let last = *self.prefix.last().unwrap();
        self.prefix.push(last + score as i64);
    }

    /// 查询 [start_time, end_time] 范围内的总分数
    pub fn total_score(&self, start_time: i32, end_time: i32) -> i64 {
        let left = self.times.partition_point(|&t| t < start_time);
        let right = self.times.partition_point(|&t| t <= end_time);
        self.prefix[right] - self.prefix[left]
    }
}
```
