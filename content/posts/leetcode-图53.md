---
title: "leetcode-图53"
date: 2026-08-01T09:50:56+08:00
tags: ["leetcode", "图"]
draft: false
---


## 交通枢纽

为了缓解「力扣嘉年华」期间的人流压力，组委会在活动期间开设了一些交通专线。path[i] = [a, b] 表示有一条从地点 a通往地点 b 的 单向 交通专线。 若存在一个地点，满足以下要求，我们则称之为 交通枢纽：

所有地点（除自身外）均有一条 单向 专线 直接 通往该地点；
该地点不存在任何 通往其他地点 的单向专线。
请返回交通专线的 交通枢纽。若不存在，则返回 -1。

注意：

对于任意一个地点，至少被一条专线连通。


```
impl Solution {
    /// 寻找交通枢纽
    ///
    /// 交通枢纽需要满足：
    /// 1. 所有其他地点都有一条单向边直接指向它（入度 = 总地点数 - 1）
    /// 2. 它没有通往任何其他地点的边（出度 = 0）
    ///
    /// # 参数
    /// - `path`: 单向边列表，path[i] = [a, b] 表示 a -> b
    ///
    /// # 返回
    /// 交通枢纽的编号，不存在则返回 -1
    ///
    /// # 复杂度
    /// - 时间：O(n + m)，n 为地点数量，m 为边数
    /// - 空间：O(n)
    pub fn transportation_hub(path: Vec<Vec<i32>>) -> i32 {
        // 使用 HashMap 动态记录地点编号，避免固定大小数组
        use std::collections::{HashMap, HashSet};

        let mut indegree = HashMap::new();
        let mut outdegree = HashMap::new();
        let mut nodes = HashSet::new();

        for edge in path {
            let u = edge[0];
            let v = edge[1];

            *indegree.entry(v).or_insert(0) += 1;
            *outdegree.entry(u).or_insert(0) += 1;
            nodes.insert(u);
            nodes.insert(v);
        }

        let total = nodes.len() as i32;
        let target_indegree = total - 1;

        // 遍历所有出现过的节点
        for &node in &nodes {
            let in_deg = indegree.get(&node).copied().unwrap_or(0);
            let out_deg = outdegree.get(&node).copied().unwrap_or(0);

            if in_deg == target_indegree && out_deg == 0 {
                return node;
            }
        }

        -1
    }
}
```
