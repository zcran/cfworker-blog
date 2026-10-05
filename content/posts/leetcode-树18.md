---
title: "leetcode-树18"
date: 2026-09-24T09:47:34+08:00
tags: ["leetcode", "树"]
draft: false
---


## 折扣价交易股票的最大利润

给你一个整数 n，表示公司中员工的数量。每位员工都分配了一个从 1 到 n 的唯一 ID ，其中员工 1 是 CEO，是每一个员工的直接或间接上司。另给你两个下标从 1 开始的整数数组 present 和 future，两个数组的长度均为 n，具体定义如下：

present[i] 表示第 i 位员工今天可以购买股票的 当前价格 。
future[i] 表示第 i 位员工明天可以卖出股票的 预期价格 。

公司的层级关系由二维整数数组 hierarchy 表示，其中 hierarchy[i] = [ui, vi] 表示员工 ui 是员工 vi 的直属上司。

此外，再给你一个整数 budget，表示可用于投资的总预算。

公司有一项折扣政策：如果某位员工的直属上司购买了公司的股票，那么该员工可以以 半价 购买股票（即 floor(present[v] / 2)）。

请返回在不超过给定预算的情况下可以获得的 最大利润 。

注意：

每只股票最多只能购买一次。
不能使用股票未来的收益来增加投资预算，购买只能依赖于 budget。


```
impl Solution {
    /// 树形背包 DP
    ///
    /// 核心思路：
    /// 对每个节点 u，定义两种状态：
    ///   dp0[b]：在“u 的父节点未购买”时，用预算 b 在 u 子树中能获得的最大利润
    ///   dp1[b]：在“u 的父节点已购买”时，用预算 b 在 u 子树中能获得的最大利润
    ///
    /// 对于节点 u：
    ///   - 购买 u 的价格：父节点已购买时为 present[u]/2，否则为 present[u]
    ///   - 利润：future[u] - 实际购买价格
    ///   - u 是否购买会影响其子节点能否享受半价
    ///
    /// 先合并所有子节点的背包（子节点用 dp0 或 dp1 取决于 u 是否购买），
    /// 再决定 u 自身是否购买。
    pub fn max_profit(
        n: i32,
        present: Vec<i32>,
        future: Vec<i32>,
        hierarchy: Vec<Vec<i32>>,
        budget: i32,
    ) -> i32 {
        let n = n as usize;
        let budget = budget as usize;

        // 建树：节点编号转为 0..n-1
        let mut children = vec![Vec::new(); n];
        for e in &hierarchy {
            let (u, v) = ((e[0] - 1) as usize, (e[1] - 1) as usize);
            children[u].push(v);
        }

        // 迭代后序遍历，避免递归深度过大
        // 第一遍：生成后序顺序
        let mut order = Vec::with_capacity(n);
        let mut stack = vec![0usize];
        while let Some(u) = stack.pop() {
            order.push(u);
            for &v in &children[u] {
                stack.push(v);
            }
        }

        // dp0[u][b], dp1[u][b]：u 的父节点未购买 / 已购买时，预算 b 的最大利润
        let mut dp0 = vec![vec![0i32; budget + 1]; n];
        let mut dp1 = vec![vec![0i32; budget + 1]; n];

        // 逆序处理（自底向上）
        for &u in order.iter().rev() {
            let cost_full = present[u] as usize;
            let cost_half = cost_full / 2;
            let profit_full = future[u] - cost_full as i32;
            let profit_half = future[u] - cost_half as i32;

            // 合并子节点的背包：
            // merged0[b]：u 不购买时，子节点只能用 dp0（子节点父节点即 u 未购买）
            // merged1[b]：u 购买时，子节点可以用 dp1（子节点父节点即 u 已购买）
            let mut merged0 = vec![0i32; budget + 1];
            let mut merged1 = vec![0i32; budget + 1];

            for &v in &children[u] {
                let child_dp0 = &dp0[v];
                let child_dp1 = &dp1[v];

                // 0/1 背包合并：逆序更新
                let mut new0 = merged0.clone();
                let mut new1 = merged1.clone();

                for b in 0..=budget {
                    // 合并到 merged0
                    for sub in 0..=b {
                        let val = merged0[b - sub] + child_dp0[sub];
                        if val > new0[b] {
                            new0[b] = val;
                        }
                    }
                    // 合并到 merged1
                    for sub in 0..=b {
                        let val = merged1[b - sub] + child_dp1[sub];
                        if val > new1[b] {
                            new1[b] = val;
                        }
                    }
                }

                merged0 = new0;
                merged1 = new1;
            }

            // 计算 dp0[u]：u 的父节点未购买，u 自身可以原价购买或不购买
            for b in 0..=budget {
                // 不购买 u
                let mut best = merged0[b];
                // 原价购买 u
                if b >= cost_full {
                    let val = merged1[b - cost_full] + profit_full;
                    if val > best {
                        best = val;
                    }
                }
                dp0[u][b] = best;
            }

            // 计算 dp1[u]：u 的父节点已购买，u 自身可以半价购买或不购买
            for b in 0..=budget {
                // 不购买 u
                let mut best = merged0[b];
                // 半价购买 u
                if b >= cost_half {
                    let val = merged1[b - cost_half] + profit_half;
                    if val > best {
                        best = val;
                    }
                }
                dp1[u][b] = best;
            }
        }

        // CEO 的父节点不存在（视为未购买），答案为 dp0[0][budget]
        dp0[0][budget]
    }
}
```
