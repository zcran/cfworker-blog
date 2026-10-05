---
title: "leetcode-贪心93"
date: 2026-10-03T12:55:40+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 全部开花的最早一天

你有 n 枚花的种子。每枚种子必须先种下，才能开始生长、开花。播种需要时间，种子的生长也是如此。给你两个下标从 0 开始的整数数组 plantTime 和 growTime ，每个数组的长度都是 n ：

plantTime[i] 是 播种 第 i 枚种子所需的 完整天数 。每天，你只能为播种某一枚种子而劳作。无须 连续几天都在种同一枚种子，但是种子播种必须在你工作的天数达到 plantTime[i] 之后才算完成。

growTime[i] 是第 i 枚种子完全种下后生长所需的 完整天数 。在它生长的最后一天 之后 ，将会开花并且永远 绽放 。

从第 0 开始，你可以按 任意 顺序播种种子。

返回所有种子都开花的 最早 一天是第几天。


```
impl Solution {
    pub fn earliest_full_bloom(plant_time: Vec<i32>, grow_time: Vec<i32>) -> i32 {
        // 贪心策略：生长时间越长的种子越早播种，避免其在最后拖后腿。
        // 将 (grow_time, plant_time) 配对后按 grow_time 降序排序。
        let mut seeds: Vec<(i32, i32)> = grow_time
            .into_iter()
            .zip(plant_time)
            .collect();
        seeds.sort_unstable_by(|a, b| b.0.cmp(&a.0));

        let mut day = 0;      // 当前已消耗的播种天数
        let mut ans = 0;      // 所有种子开花的最早一天
        for (grow, plant) in seeds {
            day += plant;             // 累计播种时间
            ans = ans.max(day + grow); // 该种子开花时间 = 播种完成日 + 生长天数
        }
        ans
    }
}
```
