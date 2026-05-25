---
title: "leetcode-拓扑排序6"
date: 2026-04-26T21:00:34+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 喧闹和富有

有一组 n 个人作为实验对象，从 0 到 n - 1 编号，其中每个人都有不同数目的钱，以及不同程度的安静值（quietness）。为了方便起见，我们将编号为 x 的人简称为 "person x "。

给你一个数组 richer ，其中 richer[i] = [ai, bi] 表示 person ai 比 person bi 更有钱。另给你一个整数数组 quiet ，其中 quiet[i] 是 person i 的安静值。richer 中所给出的数据 逻辑自洽（也就是说，在 person x 比 person y 更有钱的同时，不会出现 person y 比 person x 更有钱的情况 ）。

现在，返回一个整数数组 answer 作为答案，其中 answer[x] = y 的前提是，在所有拥有的钱肯定不少于 person x 的人中，person y 是最不安静的人（也就是安静值 quiet[y] 最小的人）。

```
impl Solution {
    /// 计算每个人的「最安静且比自己富有的人」（LeetCode 851. Loud and Rich）。
    ///
    /// 算法思路：反向建图 + DFS 记忆化搜索
    /// 1. 根据 `richer` 数组建立有向图：`graph[poor]` 存储所有比 `poor` 富有的人（即从较穷指向较富）。
    /// 2. 对每个人 `u`，定义 `answer[u]` 为 `u` 及其所有祖先（即比 `u` 富有的人）中 `quiet` 值最小的那个人的索引。
    /// 3. 通过 DFS 后序遍历，先递归计算所有邻居（更富有的人）的答案，然后从 `u` 和所有邻居的答案中选择 `quiet` 最小的人。
    /// 4. 使用记忆化数组 `memo` 存储已计算的结果（`-1` 表示未计算）。
    pub fn loud_and_rich(richer: Vec<Vec<i32>>, quiet: Vec<i32>) -> Vec<i32> {
        let n = quiet.len();
        // 构建邻接表：graph[poor] = 所有比 poor 富有的人
        let mut graph = vec![Vec::new(); n];
        for edge in richer {
            let poor = edge[1] as usize;   // edge[1] 是较穷的人
            let rich = edge[0] as usize;   // edge[0] 是较富的人
            graph[poor].push(rich);
        }

        // 记忆化数组：-1 表示尚未计算，否则存储对应人的索引（作为 i32）
        let mut memo = vec![-1; n];

        for i in 0..n {
            if memo[i] == -1 {
                Self::dfs(&graph, &quiet, i, &mut memo);
            }
        }

        memo
    }

    /// 深度优先搜索计算节点 `u` 的答案（即 `u` 及其所有祖先中 quiet 值最小的人）。
    ///
    /// * `graph` - 邻接表，`graph[u]` 是所有比 `u` 富有的人。
    /// * `quiet` - 每个人的安静值。
    /// * `u` - 当前节点索引。
    /// * `memo` - 记忆化数组。
    fn dfs(graph: &[Vec<usize>], quiet: &[i32], u: usize, memo: &mut Vec<i32>) {
        // 如果已经计算过，直接返回
        if memo[u] != -1 {
            return;
        }

        // 先递归处理所有比 u 富有的人（即邻居），确保它们的 memo 已经填好
        for &v in &graph[u] {
            if memo[v] == -1 {
                Self::dfs(graph, quiet, v, memo);
            }
        }

        // 使用迭代器从「自身」和「所有邻居的答案」中选择 quiet 值最小的索引
        // `std::iter::once(u)` 产生自身索引，`chain` 拼接邻居的答案（需转换为 usize）
        let best = std::iter::once(u)
            .chain(graph[u].iter().map(|&v| memo[v] as usize))
            .min_by_key(|&idx| quiet[idx])
            .unwrap(); // 至少包含自身，所以 unwrap 安全

        memo[u] = best as i32;
    }
}
```
