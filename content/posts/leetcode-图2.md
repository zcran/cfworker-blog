---
title: "leetcode-图2"
date: 2026-07-27T10:01:35+08:00
tags: ["leetcode", "图"]
draft: false
---


## 重新安排行程

给你一份航线列表 tickets ，其中 tickets[i] = [fromi, toi] 表示飞机出发和降落的机场地点。请你对该行程进行重新规划排序。

所有这些机票都属于一个从 JFK（肯尼迪国际机场）出发的先生，所以该行程必须从 JFK 开始。如果存在多种有效的行程，请你按字典排序返回最小的行程组合。

例如，行程 ["JFK", "LGA"] 与 ["JFK", "LGB"] 相比就更小，排序更靠前。
假定所有机票至少存在一种合理的行程。且所有的机票 必须都用一次 且 只能用一次。


```
use std::collections::HashMap;

impl Solution {
    /// 重新规划行程，返回字典序最小的行程组合
    ///
    /// # 算法思路
    /// 使用 Hierholzer 算法寻找欧拉路径：
    /// 1. 构建邻接表（出发地 -> 目的地列表）
    /// 2. 对每个目的地的列表按**字典序降序**排序（配合栈实现升序弹出）
    /// 3. DFS 深度优先遍历，使用 while 循环不断弹出目的地
    /// 4. 最后反转结果即为字典序最小的欧拉路径
    pub fn find_itinerary(tickets: Vec<Vec<String>>) -> Vec<String> {
        // 邻接表：出发机场 -> 可到达的机场列表（允许重复）
        let mut graph: HashMap<String, Vec<String>> = HashMap::new();

        // 构建图：将每个机票的目的地添加到对应出发地的列表中
        for ticket in &tickets {
            let from = ticket[0].clone();
            let to = ticket[1].clone();
            graph.entry(from).or_insert_with(Vec::new).push(to);
        }

        // 对每个出发地的目的地列表按降序排序
        // 因为后续使用 pop() 取出最后一个元素，这样就能保证按升序访问
        for destinations in graph.values_mut() {
            destinations.sort_unstable_by(|a, b| b.cmp(a));
        }

        let mut route = Vec::new();

        // 深度优先搜索：从 JFK 出发，构建欧拉路径
        fn dfs(
            airport: String,
            graph: &mut HashMap<String, Vec<String>>,
            route: &mut Vec<String>
        ) {
            // 当当前机场还有可用的目的地时，继续深入探索
            while let Some(next) = graph.get_mut(&airport)
                                        .and_then(|destinations| destinations.pop()) {
                dfs(next, graph, route);
            }
            // 所有目的地都已访问完毕，将当前机场加入路径
            // 此时加入的顺序是逆序，需要在最后反转
            route.push(airport);
        }

        dfs("JFK".to_string(), &mut graph, &mut route);

        // 反转得到正确的行程顺序
        route.reverse();
        route
    }
}
```
