---
title: "leetcode-数组21"
date: 2026-10-10T20:38:41+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 在既定时间做作业的学生人数

给你两个整数数组 startTime（开始时间）和 endTime（结束时间），并指定一个整数 queryTime 作为查询时间。

已知，第 i 名学生在 startTime[i] 时开始写作业并于 endTime[i] 时完成作业。

请返回在查询时间 queryTime 时正在做作业的学生人数。形式上，返回能够使 queryTime 处于区间 [startTime[i], endTime[i]]（含）的学生人数。


```
impl Solution {
    /// 统计在 `query_time` 时刻正在做作业的学生人数。
    ///
    /// 对每个学生，判断 `query_time` 是否落在闭区间
    /// `[start_time[i], end_time[i]]` 内，统计满足条件的人数。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn busy_student(start_time: Vec<i32>, end_time: Vec<i32>, query_time: i32) -> i32 {
        start_time
            .iter()
            .zip(end_time.iter())
            .filter(|&(&s, &e)| s <= query_time && query_time <= e)
            .count() as i32
    }
}
```
