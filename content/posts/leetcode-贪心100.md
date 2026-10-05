---
title: "leetcode-贪心100"
date: 2026-10-03T12:55:40+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 装满石头的背包的最大数量

现有编号从 0 到 n - 1 的 n 个背包。给你两个下标从 0 开始的整数数组 capacity 和 rocks 。第 i 个背包最大可以装 capacity[i] 块石头，当前已经装了 rocks[i] 块石头。另给你一个整数 additionalRocks ，表示你可以放置的额外石头数量，石头可以往 任意 背包中放置。

请你将额外的石头放入一些背包中，并返回放置后装满石头的背包的 最大 数量。


```
impl Solution {
    pub fn maximum_bags(mut capacity: Vec<i32>, rocks: Vec<i32>, additional_rocks: i32) -> i32 {
        // 计算每个背包还差多少装满，按缺口从小到大排序
        for i in 0..capacity.len() {
            capacity[i] -= rocks[i];
        }
        capacity.sort_unstable();

        // 贪心：优先填缺口最小的背包，能填几个就填几个
        let mut remaining = additional_rocks;
        let mut full = 0;
        for gap in capacity {
            if gap > remaining {
                break;
            }
            remaining -= gap;
            full += 1;
        }
        full
    }
}
```
