---
title: "leetcode-双指针118"
date: 2026-09-05T09:53:08+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 最早完成陆地和水上游乐设施的时间 I

给你两种类别的游乐园项目：陆地游乐设施 和 水上游乐设施。

· 陆地游乐设施
    · landStartTime[i] – 第 i 个陆地游乐设施最早可以开始的时间。
    · landDuration[i] – 第 i 个陆地游乐设施持续的时间。
· 水上游乐设施
    · waterStartTime[j] – 第 j 个水上游乐设施最早可以开始的时间。
    · waterDuration[j] – 第 j 个水上游乐设施持续的时间。

一位游客必须从 每个 类别中体验 恰好一个 游乐设施，顺序 不限 。

· 游乐设施可以在其开放时间开始，或 之后任意时间 开始。
· 如果一个游乐设施在时间 t 开始，它将在时间 t + duration 结束。
· 完成一个游乐设施后，游客可以立即乘坐另一个（如果它已经开放），或者等待它开放。

返回游客完成这两个游乐设施的 最早可能时间 。


```
impl Solution {
    /// 从陆地和水上各选一个设施（顺序任意），返回完成两者的最早时间。
    ///
    /// 思路：顺序只有两种——先陆地后水上，或先水上后陆地。
    /// - 先陆地：完成陆地的时间 = startL + durL；水上的开始时间取 max(该时间, startW)，再加水上时长。
    /// - 先水上：对称处理。
    /// 对所有组合取最小值。
    ///
    /// 时间复杂度 O(n * m)，空间复杂度 O(1)。
    pub fn earliest_finish_time(
        land_start_time: Vec<i32>,
        land_duration: Vec<i32>,
        water_start_time: Vec<i32>,
        water_duration: Vec<i32>,
    ) -> i32 {
        let mut ans = i32::MAX;

        for i in 0..land_start_time.len() {
            let land_end = land_start_time[i] + land_duration[i];
            for j in 0..water_start_time.len() {
                let water_end = water_start_time[j] + water_duration[j];

                // 先陆地后水上
                let finish1 = land_end.max(water_start_time[j]) + water_duration[j];
                // 先水上后陆地
                let finish2 = water_end.max(land_start_time[i]) + land_duration[i];

                ans = ans.min(finish1).min(finish2);
            }
        }

        ans
    }
}
```
