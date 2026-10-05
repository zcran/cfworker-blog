---
title: "leetcode-双指针119"
date: 2026-09-05T09:53:08+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 最早完成陆地和水上游乐设施的时间 II

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
    /// 给定「先玩第一类、再玩第二类」，返回最早完成时间。
    ///
    /// 1. 先从第一类中挑选完成时间最早的设施（只考虑其开放时间即可，无需等待其他）。
    /// 2. 再在第二类中挑选，使「等待开放 + 游玩时长」后的完成时间最小。
    fn solve(s1: &[i32], d1: &[i32], s2: &[i32], d2: &[i32]) -> i32 {
        // 第一类设施的最早完成时间
        let finish1 = s1
            .iter()
            .zip(d1)
            .map(|(&s, &d)| s + d)
            .min()
            .unwrap();

        // 第二类设施：开始时间取 max(开放时间, finish1)，再叠加时长
        s2.iter()
            .zip(d2)
            .map(|(&s, &d)| s.max(finish1) + d)
            .min()
            .unwrap()
    }

    /// 从陆地和水上各选一个设施（顺序任意），返回完成两者的最早时间。
    ///
    /// 顺序只有两种：先陆地后水上、先水上后陆地，分别计算后取较小值。
    ///
    /// 时间复杂度 O(n + m)，空间复杂度 O(1)。
    pub fn earliest_finish_time(
        land_start_time: Vec<i32>,
        land_duration: Vec<i32>,
        water_start_time: Vec<i32>,
        water_duration: Vec<i32>,
    ) -> i32 {
        let land_water = Self::solve(
            &land_start_time,
            &land_duration,
            &water_start_time,
            &water_duration,
        );
        let water_land = Self::solve(
            &water_start_time,
            &water_duration,
            &land_start_time,
            &land_duration,
        );

        land_water.min(water_land)
    }
}
```
