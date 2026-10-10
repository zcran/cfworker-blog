---
title: "leetcode-哈希表36"
date: 2026-10-09T20:07:57+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 旅行终点站

给你一份旅游线路图，该线路图中的旅行线路用数组 paths 表示，其中 paths[i] = [cityAi, cityBi] 表示该线路将会从 cityAi 直接前往 cityBi 。请你找出这次旅行的终点站，即没有任何可以通往其他城市的线路的城市。

题目数据保证线路图会形成一条不存在循环的线路，因此恰有一个旅行终点站。


```
use std::collections::HashSet;

impl Solution {
    pub fn dest_city(paths: Vec<Vec<String>>) -> String {
        // 终点站是只作为目的地、从不作为出发地的城市。
        // 先收集所有出发城市，再找未出现在其中的目的地。
        let starts: HashSet<&str> = paths.iter().map(|p| p[0].as_str()).collect();
        paths
            .iter()
            .find(|p| !starts.contains(p[1].as_str()))
            .map(|p| p[1].clone())
            .unwrap()
    }
}
```
