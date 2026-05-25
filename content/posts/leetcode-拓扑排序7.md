---
title: "leetcode-拓扑排序7"
date: 2026-04-26T21:00:34+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 项目管理

有 n 个项目，每个项目或者不属于任何小组，或者属于 m 个小组之一。group[i] 表示第 i 个项目所属的小组，如果第 i 个项目不属于任何小组，则 group[i] 等于 -1。项目和小组都是从零开始编号的。可能存在小组不负责任何项目，即没有任何项目属于这个小组。

请你帮忙按要求安排这些项目的进度，并返回排序后的项目列表：

同一小组的项目，排序后在列表中彼此相邻。
项目之间存在一定的依赖关系，我们用一个列表 beforeItems 来表示，其中 beforeItems[i] 表示在进行第 i 个项目前（位于第 i 个项目左侧）应该完成的所有项目。
如果存在多个解决方案，只需要返回其中任意一个即可。如果没有合适的解决方案，就请返回一个 空列表 。

```
use std::collections::VecDeque;

/// 解决 LeetCode 1203 "Sort Items by Groups Respecting Dependencies"
/// 算法：两级拓扑排序 —— 先对组排序，再在每个组内对项目排序。
impl Solution {
    pub fn sort_items(
        n: i32,
        m: i32,
        mut group: Vec<i32>,
        before_items: Vec<Vec<i32>>,
    ) -> Vec<i32> {
        // ---------- 阶段 1：规范化组 ID ----------
        // 为所有 group == -1 的项目分配新的独立组 ID
        let (n, mut m) = (n as usize, m as usize);
        let mut group = group;
        let mut group_item_counts = vec![0; m];
        for g in &mut group {
            if *g == -1 {
                *g = m as i32;
                group_item_counts.push(1); // 新组，初始化计数为 1
                m += 1;
            } else {
                group_item_counts[*g as usize] += 1;
            }
        }
        let total_groups = m;

        // ---------- 阶段 2：构建组之间的依赖图 ----------
        let mut group_indegree = vec![0; total_groups];
        let mut group_graph = vec![Vec::new(); total_groups];

        for (i, item_deps) in before_items.iter().enumerate() {
            let cur_group = group[i] as usize;
            for &before_item in item_deps {
                let before_group = group[before_item as usize] as usize;
                if cur_group != before_group {
                    group_indegree[cur_group] += 1;
                    group_graph[before_group].push(cur_group);
                }
            }
        }

        // ---------- 阶段 3：对组进行拓扑排序 ----------
        let group_order = Self::topological_sort(&mut group_indegree, &group_graph)
            .unwrap_or_else(Vec::new);
        if group_order.is_empty() {
            return vec![];
        }

        // ---------- 阶段 4：构建项目之间的依赖图 ----------
        let mut item_indegree = vec![0; n];
        let mut item_graph = vec![Vec::new(); n];
        for (i, deps) in before_items.iter().enumerate() {
            item_indegree[i] = deps.len();
            for &before in deps {
                item_graph[before as usize].push(i);
            }
        }

        // ---------- 阶段 5：按组准备项目队列 ----------
        // 每个组有一个双端队列，存放当前入度为 0 的项目
        let mut group_queues = vec![VecDeque::new(); total_groups];
        for i in 0..n {
            if item_indegree[i] == 0 {
                let g = group[i] as usize;
                group_queues[g].push_back(i);
            }
        }

        // ---------- 阶段 6：按照组的顺序，依次处理每个组内的项目 ----------
        let mut result = Vec::with_capacity(n);
        for &g in &group_order {
            let mut processed_in_group = 0;
            let expected_in_group = group_item_counts[g];

            while let Some(item) = group_queues[g].pop_front() {
                processed_in_group += 1;
                result.push(item as i32);

                // 减少后续项目的入度，若变为 0 则放入所属组的队列
                for &next in &item_graph[item] {
                    item_indegree[next] -= 1;
                    if item_indegree[next] == 0 {
                        let next_group = group[next] as usize;
                        group_queues[next_group].push_back(next);
                    }
                }
            }

            // 如果该组内的项目没有全部处理，说明存在环
            if processed_in_group != expected_in_group {
                return vec![];
            }
        }

        result
    }

    /// 拓扑排序（Kahn 算法）
    /// 返回排序结果；如果存在环则返回 `None`。
    fn topological_sort(indegree: &mut [i32], graph: &[Vec<usize>]) -> Option<Vec<usize>> {
        let mut queue = VecDeque::new();
        let mut order = Vec::with_capacity(indegree.len());

        // 初始入度为 0 的节点
        for (i, &deg) in indegree.iter().enumerate() {
            if deg == 0 {
                queue.push_back(i);
                order.push(i);
            }
        }

        while let Some(u) = queue.pop_front() {
            for &v in &graph[u] {
                indegree[v] -= 1;
                if indegree[v] == 0 {
                    queue.push_back(v);
                    order.push(v);
                }
            }
        }

        if order.len() == indegree.len() {
            Some(order)
        } else {
            None // 存在环
        }
    }
}
```
