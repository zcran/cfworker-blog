---
title: "leetcode-图51"
date: 2026-08-01T09:50:11+08:00
tags: ["leetcode", "图"]
draft: false
---


## 夺回据点

欢迎各位勇者来到力扣城，本次试炼主题为「夺回据点」。

魔物了占领若干据点，这些据点被若干条道路相连接，roads[i] = [x, y] 表示编号 x、y 的两个据点通过一条道路连接。

现在勇者要将按照以下原则将这些据点逐一夺回：

在开始的时候，勇者可以花费资源先夺回一些据点，初始夺回第 j 个据点所需消耗的资源数量为 cost[j]

接下来，勇者在不消耗资源情况下，每次可以夺回一个和「已夺回据点」相连接的魔物据点，并对其进行夺回

注：为了防止魔物暴动，勇者在每一次夺回据点后（包括花费资源夺回据点后），需要保证剩余的所有魔物据点之间是相连通的（不经过「已夺回据点」）。

请返回勇者夺回所有据点需要消耗的最少资源数量。


```
impl Solution {
    /// 夺回所有据点所需的最少资源
    ///
    /// # 算法思路
    /// 1. 使用 Tarjan 算法找出所有割点（关节点）和点双连通分量（V-DCC）
    /// 2. 对于每个 V-DCC：
    ///    - 如果只连接一个割点，则是"叶子"节点，必须选一个非割点夺回
    ///    - 如果连接 ≥2 个割点，则不需要消耗资源夺回（可以通过已夺回据点逐步攻占）
    /// 3. 所有叶子 V-DCC 中必须各选一个点，选择每个分量中 cost 最小的非割点
    /// 4. 如果整图是单一 V-DCC，只需选择全局 cost 最小的点即可
    ///
    /// # 复杂度
    /// - 时间：O(n + m)，m 为边数
    /// - 空间：O(n + m)
    pub fn minimum_cost(cost: Vec<i32>, roads: Vec<Vec<i32>>) -> i64 {
        let n = cost.len();
        if n == 1 {
            return cost[0] as i64;
        }

        // 构建邻接表
        let mut graph = vec![Vec::new(); n];
        for road in roads {
            let u = road[0] as usize;
            let v = road[1] as usize;
            graph[u].push(v);
            graph[v].push(u);
        }

        // Tarjan 算法求割点和点双连通分量
        let mut dfn = vec![0; n];
        let mut low = vec![0; n];
        let mut is_cut = vec![false; n];
        let mut stack = Vec::new();
        let mut dcc = Vec::new(); // 点双连通分量
        let mut clk = 0;

        fn tarjan(
            u: usize,
            parent: usize,
            clk: &mut i32,
            graph: &[Vec<usize>],
            dfn: &mut [i32],
            low: &mut [i32],
            is_cut: &mut [bool],
            stack: &mut Vec<usize>,
            dcc: &mut Vec<Vec<usize>>,
        ) {
            *clk += 1;
            dfn[u] = *clk;
            low[u] = *clk;
            stack.push(u);

            let mut child_count = 0;
            for &v in &graph[u] {
                if v == parent {
                    continue;
                }
                if dfn[v] == 0 {
                    child_count += 1;
                    tarjan(v, u, clk, graph, dfn, low, is_cut, stack, dcc);
                    low[u] = low[u].min(low[v]);

                    // 割点判断：u 是根且 child_count > 1，或 u 非根且 low[v] >= dfn[u]
                    if (parent == usize::MAX && child_count > 1) || (parent != usize::MAX && low[v] >= dfn[u]) {
                        is_cut[u] = true;
                    }

                    // 如果 low[v] >= dfn[u]，说明 v 及子树构成一个点双连通分量
                    if low[v] >= dfn[u] {
                        let mut comp = Vec::new();
                        loop {
                            let top = stack.pop().unwrap();
                            comp.push(top);
                            if top == v {
                                break;
                            }
                        }
                        comp.push(u); // u 属于该点双
                        dcc.push(comp);
                    }
                } else {
                    low[u] = low[u].min(dfn[v]);
                }
            }
        }

        // 从 0 开始 DFS（图是连通的）
        tarjan(0, usize::MAX, &mut clk, &graph, &mut dfn, &mut low, &mut is_cut, &mut stack, &mut dcc);

        // 如果只有一个点双连通分量，取全局最小 cost
        if dcc.len() == 1 {
            return *cost.iter().min().unwrap() as i64;
        }

        // 收集所有叶子 V-DCC（只连接一个割点的分量）中的最小非割点 cost
        let mut leaf_costs = Vec::new();

        for comp in &dcc {
            let mut cut_count = 0;
            let mut min_non_cut = i32::MAX;

            for &node in comp {
                if is_cut[node] {
                    cut_count += 1;
                } else {
                    min_non_cut = min_non_cut.min(cost[node]);
                }
            }

            // 只连接一个割点的分量是叶子节点，必须从中选一个点消耗资源夺回
            if cut_count == 1 && min_non_cut != i32::MAX {
                leaf_costs.push(min_non_cut as i64);
            }
        }

        // 排序后，去掉最大值（最后一个分量可以保留一个初始据点不需要资源）
        leaf_costs.sort_unstable();
        // 需要消耗资源的数量 = leaf_costs.len() - 1（最后一个不用资源）
        // 但每个叶子必须选一个点，除了最大的一个可以保留
        let ans: i64 = leaf_costs.iter().take(leaf_costs.len().saturating_sub(1)).sum();

        // 如果所有叶子分量 cost 总和为 0，但 n > 1 时仍需至少消耗一次资源
        if ans == 0 && n > 1 {
            // 理论上不会发生，因为每个叶子分量至少有一个非割点 cost >= 0
            // 但为安全处理边界情况，取全局最小 cost
            return *cost.iter().min().unwrap() as i64;
        }

        ans
    }
}
```
