---
title: "leetcode-图22"
date: 2026-07-27T10:01:36+08:00
tags: ["leetcode", "图"]
draft: false
---


## 道路的最大总重要性

给你一个整数 n ，表示一个国家里的城市数目。城市编号为 0 到 n - 1 。

给你一个二维整数数组 roads ，其中 roads[i] = [ai, bi] 表示城市 ai 和 bi 之间有一条 双向 道路。

你需要给每个城市安排一个从 1 到 n 之间的整数值，且每个值只能被使用 一次 。道路的 重要性 定义为这条道路连接的两座城市数值 之和 。

请你返回在最优安排下，所有道路重要性 之和 最大 为多少。


```
impl Solution {
    pub fn maximum_importance(n: i32, roads: Vec<Vec<i32>>) -> i64 {
        let n = n as usize;

        // 统计每个城市的度数（连接的道路数量）
        let mut deg = vec![0; n];
        for road in roads {
            let (u, v) = (road[0] as usize, road[1] as usize);
            deg[u] += 1;
            deg[v] += 1;
        }

        // 度数越高，分配越大的数值，使总和最大
        deg.sort_unstable();

        // 贪心分配：最小度数配最小数值，最大度数配最大数值
        deg.iter()
            .enumerate()
            .map(|(i, &d)| (i as i64 + 1) * d as i64)
            .sum()
    }
}
```
