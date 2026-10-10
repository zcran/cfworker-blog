---
title: "leetcode-数组41"
date: 2026-10-10T20:38:43+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 满足目标工作时长的员工数目

公司里共有 n 名员工，按从 0 到 n - 1 编号。每个员工 i 已经在公司工作了 hours[i] 小时。

公司要求每位员工工作 至少 target 小时。

给你一个下标从 0 开始、长度为 n 的非负整数数组 hours 和一个非负整数 target 。

请你用整数表示并返回工作至少 target 小时的员工数。


```
impl Solution {
    /// 统计工作至少 `target` 小时的员工数。
    ///
    /// 遍历 `hours`，用 `filter` 筛选 `>= target` 的元素并计数。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn number_of_employees_who_met_target(hours: Vec<i32>, target: i32) -> i32 {
        hours.iter().filter(|&&h| h >= target).count() as i32
    }
}
```
