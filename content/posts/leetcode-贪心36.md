---
title: "leetcode-贪心36"
date: 2026-10-03T12:55:36+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 两地调度

公司计划面试 2n 人。给你一个数组 costs ，其中 costs[i] = [aCosti, bCosti] 。第 i 人飞往 a 市的费用为 aCosti ，飞往 b 市的费用为 bCosti 。

返回将每个人都飞到 a 、b 中某座城市的最低费用，要求每个城市都有 n 人抵达。


```
impl Solution {
    pub fn two_city_sched_cost(costs: Vec<Vec<i32>>) -> i32 {
        // 先假设所有人都飞 b 市，再选出 n 人改飞 a 市
        // 改飞 a 市的净代价为 costs[i][0] - costs[i][1]，选择净代价最小的 n 人
        let mut diffs: Vec<i32> = costs.iter().map(|c| c[0] - c[1]).collect();
        diffs.sort_unstable();

        let base: i32 = costs.iter().map(|c| c[1]).sum();
        base + diffs[..costs.len() / 2].iter().sum::<i32>()
    }
}
```
