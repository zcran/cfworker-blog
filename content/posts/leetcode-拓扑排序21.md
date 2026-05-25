---
title: "leetcode-拓扑排序21"
date: 2026-04-26T21:00:35+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 收集树中金币

给你一个 n 个节点的无向无根树，节点编号从 0 到 n - 1 。给你整数 n 和一个长度为 n - 1 的二维整数数组 edges ，其中 edges[i] = [ai, bi] 表示树中节点 ai 和 bi 之间有一条边。再给你一个长度为 n 的数组 coins ，其中 coins[i] 可能为 0 也可能为 1 ，1 表示节点 i 处有一个金币。

一开始，你需要选择树中任意一个节点出发。你可以执行下述操作任意次：

收集距离当前节点距离为 2 以内的所有金币，或者
移动到树中一个相邻节点。
你需要收集树中所有的金币，并且回到出发节点，请你返回最少经过的边数。

如果你多次经过一条边，每一次经过都会给答案加一。

```
impl Solution {
    /// 计算收集所有金币所需的最小移动步数。
    /// 给定一棵树，节点上标有 0（无金币）或 1（有金币）。每次可以移动到相邻节点，收集经过节点的金币。
    /// 最终需要返回起点（但步数计算为往返路径）。算法通过两次剪枝得到必须遍历的子图，其边数乘以 2 即为答案。
    ///
    /// # 参数
    /// - `coins`: 每个节点是否有金币（0/1）
    /// - `edges`: 树的边，每条边为 `[u, v]`
    ///
    /// # 返回
    /// 最小移动步数。
    pub fn collect_the_coins(coins: Vec<i32>, edges: Vec<Vec<i32>>) -> i32 {
        let n = coins.len();
        // 构建邻接表并统计初始度数
        let mut graph = vec![Vec::new(); n];
        let mut degree = vec![0; n];
        for edge in &edges {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            graph[u].push(v);
            graph[v].push(u);
            degree[u] += 1;
            degree[v] += 1;
        }

        // 剩余需要走过的边数（初始为整棵树的边数）
        let mut remaining_edges = n as i32 - 1;

        // ---------- 第一阶段：移除所有**不含金币的叶子节点**及其链路 ----------
        // 找到所有度为 1 且无金币的叶子节点
        let mut queue: Vec<usize> = (0..n)
            .filter(|&i| degree[i] == 1 && coins[i] == 0)
            .collect();

        while let Some(leaf) = queue.pop() {
            // 删除 leaf 与其唯一邻居的边
            remaining_edges -= 1;
            for &neighbor in &graph[leaf] {
                degree[neighbor] -= 1;
                // 如果邻居因此变成度为 1 且同样无金币，则加入队列继续剪枝
                if degree[neighbor] == 1 && coins[neighbor] == 0 {
                    queue.push(neighbor);
                }
            }
        }

        // ---------- 第二阶段：处理**有金币的叶子节点**，并尝试移除其父节点 ----------
        // 收集所有度为 1 且含有金币的叶子节点
        let coin_leaves: Vec<usize> = (0..n)
            .filter(|&i| degree[i] == 1 && coins[i] == 1)
            .collect();

        // 删除这些叶子节点到其父节点的边
        remaining_edges -= coin_leaves.len() as i32;

        for &leaf in &coin_leaves {
            for &neighbor in &graph[leaf] {
                degree[neighbor] -= 1;
                // 如果父节点因此变成叶子（度为 1），则删除父节点到其父节点的边
                // （注意：这里只处理一层，不再递归，符合原算法逻辑）
                if degree[neighbor] == 1 {
                    remaining_edges -= 1;
                }
            }
        }

        // 每条剩余的边需要走两遍（来回），答案不能为负数
        (remaining_edges * 2).max(0)
    }
}
```
