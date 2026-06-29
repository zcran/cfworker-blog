---
title: "leetcode-有序集合20"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 花期内花的数目

给你一个下标从 0 开始的二维整数数组 flowers ，其中 flowers[i] = [starti, endi] 表示第 i 朵花的 花期 从 starti 到 endi （都 包含）。同时给你一个下标从 0 开始大小为 n 的整数数组 people ，people[i] 是第 i 个人来看花的时间。

请你返回一个大小为 n 的整数数组 answer ，其中 answer[i]是第 i 个人到达时在花期内花的 数目 。


```
impl Solution {
    /// 计算每个人到达时正在盛开的花朵数量
    ///
    /// # 思路
    /// 对于每个人，答案为：
    /// 开始时间 <= 到达时间 的花朵数 - 结束时间 < 到达时间 的花朵数
    ///
    /// # 时间复杂度
    /// O((n + m) log n)，其中 n 是花朵数，m 是人数
    ///
    /// # 空间复杂度
    /// O(n)
    pub fn full_bloom_flowers(flowers: Vec<Vec<i32>>, people: Vec<i32>) -> Vec<i32> {
        // 收集所有开始时间和结束时间并排序
        let mut starts: Vec<i32> = flowers.iter().map(|f| f[0]).collect();
        let mut ends: Vec<i32> = flowers.iter().map(|f| f[1]).collect();
        starts.sort_unstable();
        ends.sort_unstable();

        // 对每个人计算：开始时间 <= t 的数量 - 结束时间 < t 的数量
        people
            .into_iter()
            .map(|t| {
                // 花朵开始时间 <= t 的数量（花朵已开始）
                let bloomed = starts.partition_point(|&x| x <= t);
                // 花朵结束时间 < t 的数量（花朵已凋谢）
                let faded = ends.partition_point(|&x| x < t);
                (bloomed - faded) as i32
            })
            .collect()
    }
}
```
