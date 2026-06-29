---
title: "leetcode-有序集合36"
date: 2026-06-14T10:47:42+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 电网维护

给你一个整数 c，表示 c 个电站，每个电站有一个唯一标识符 id，从 1 到 c 编号。

这些电站通过 n 条 双向 电缆互相连接，表示为一个二维数组 connections，其中每个元素 connections[i] = [ui, vi] 表示电站 ui 和电站 vi 之间的连接。直接或间接连接的电站组成了一个 电网 。

最初，所有 电站均处于在线（正常运行）状态。

另给你一个二维数组 queries，其中每个查询属于以下 两种类型之一 ：

· [1, x]：请求对电站 x 进行维护检查。如果电站 x 在线，则它自行解决检查。如果电站 x 已离线，则检查由与 x 同一 电网 中 编号最小 的在线电站解决。如果该电网中 不存在 任何 在线 电站，则返回 -1。

· [2, x]：电站 x 离线（即变为非运行状态）。

返回一个整数数组，表示按照查询中出现的顺序，所有类型为 [1, x] 的查询结果。

注意：电网的结构是固定的；离线（非运行）的节点仍然属于其所在的电网，且离线操作不会改变电网的连接性。


```
use std::collections::HashMap;

struct DSU {
    parent: Vec<usize>,
    rank: Vec<usize>, // 用于按秩合并，优化性能
}

impl DSU {
    fn new(size: usize) -> Self {
        let mut parent = vec![0; size];
        for i in 0..size {
            parent[i] = i;
        }
        Self {
            parent,
            rank: vec![0; size],
        }
    }

    // 路径压缩的查找
    fn find(&mut self, x: usize) -> usize {
        if self.parent[x] != x {
            self.parent[x] = self.find(self.parent[x]);
        }
        self.parent[x]
    }

    // 按秩合并
    fn join(&mut self, u: usize, v: usize) {
        let root_u = self.find(u);
        let root_v = self.find(v);
        if root_u == root_v {
            return;
        }
        // 按秩合并：将秩小的树接到秩大的树下
        if self.rank[root_u] < self.rank[root_v] {
            self.parent[root_u] = root_v;
        } else if self.rank[root_u] > self.rank[root_v] {
            self.parent[root_v] = root_u;
        } else {
            self.parent[root_v] = root_u;
            self.rank[root_u] += 1;
        }
    }
}

impl Solution {
    pub fn process_queries(c: i32, connections: Vec<Vec<i32>>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let c = c as usize;
        let mut dsu = DSU::new(c + 1);

        // 构建并查集，确定电网结构
        for conn in &connections {
            dsu.join(conn[0] as usize, conn[1] as usize);
        }

        // 记录每个电站的在线状态
        let mut online = vec![true; c + 1];
        // 记录每个电站被要求离线的次数（用于处理重复离线）
        let mut offline_count = vec![0; c + 1];

        // 第一遍遍历：处理所有离线操作，标记离线状态
        for q in &queries {
            if q[0] == 2 {
                let x = q[1] as usize;
                online[x] = false;
                offline_count[x] += 1;
            }
        }

        // 计算每个电网的编号最小的在线电站
        let mut min_online: HashMap<usize, i32> = HashMap::new();

        for i in 1..=c {
            let root = dsu.find(i);
            if online[i] {
                // 只记录在线的电站
                let entry = min_online.entry(root).or_insert(i as i32);
                if i < *entry as usize {
                    *entry = i as i32;
                }
            }
        }

        let mut ans = Vec::new();

        // 反向处理查询，维护在线状态的变化
        for i in (0..queries.len()).rev() {
            let op = queries[i][0];
            let x = queries[i][1] as usize;
            let root = dsu.find(x);

            if op == 1 {
                // 查询类型1：获取负责的电站
                let result = if online[x] {
                    // 电站x在线，由自己处理
                    x as i32
                } else {
                    // 电站x离线，找电网中编号最小的在线电站
                    // 如果不存在在线电站，min_online中可能没有该root的条目
                    *min_online.get(&root).unwrap_or(&-1)
                };
                ans.push(result);
            } else {
                // 查询类型2：离线操作（反向处理时实际上是恢复在线）
                if offline_count[x] > 1 {
                    // 有多次离线操作，只减少计数
                    offline_count[x] -= 1;
                } else {
                    // 最后一次离线操作，恢复在线
                    online[x] = true;
                    // 更新该电网的最小在线电站
                    let entry = min_online.entry(root).or_insert(i32::MAX);
                    if x < *entry as usize {
                        *entry = x as i32;
                    }
                }
            }
        }

        // 反转结果，恢复原始顺序
        ans.reverse();
        ans
    }
}
```
