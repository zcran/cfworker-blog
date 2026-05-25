---
title: "leetcode-记忆化30"
date: 2026-05-06T20:22:34+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 收集所有金币可获得的最大积分

有一棵由 n 个节点组成的无向树，以 0  为根节点，节点编号从 0 到 n - 1 。给你一个长度为 n - 1 的二维 整数 数组 edges ，其中 edges[i] = [ai, bi] 表示在树上的节点 ai 和 bi 之间存在一条边。另给你一个下标从 0 开始、长度为 n 的数组 coins 和一个整数 k ，其中 coins[i] 表示节点 i 处的金币数量。

从根节点开始，你必须收集所有金币。要想收集节点上的金币，必须先收集该节点的祖先节点上的金币。

节点 i 上的金币可以用下述方法之一进行收集：

· 收集所有金币，得到共计 coins[i] - k 点积分。如果 coins[i] - k 是负数，你将会失去 abs(coins[i] - k) 点积分。
· 收集所有金币，得到共计 floor(coins[i] / 2) 点积分。如果采用这种方法，节点 i 子树中所有节点 j 的金币数 coins[j] 将会减少至 floor(coins[j] / 2) 。

返回收集 所有 树节点的金币之后可以获得的最大积分。


```
impl Solution {
    /// 计算在树上进行“右移”操作后能获得的最大分数（LeetCode 2920）
    ///
    /// 每个节点上有一些金币，可以反复进行“右移一位”（相当于除以2向下取整），每右移一次，后续操作基于新的值。
    /// 对于每个节点，有两种选择：
    /// - 不右移（仍用当前值 `val`），收益为 `val - k`
    /// - 右移一次（不管之前右移几次，这里再右移一次），收益为 `val >> 1`（即 `val / 2`，向下取整），且不扣 k
    /// 树形结构，每个节点需对每个子节点独立决策。求从根节点出发的最大总收益。
    ///
    /// # 参数
    /// - `edges`: 无向树的边列表
    /// - `coins`: 每个节点的初始金币数
    /// - `k`: 每次“不右移”时扣除的固定代价
    ///
    /// # 返回
    /// 最大总收益
    pub fn maximum_points(edges: Vec<Vec<i32>>, coins: Vec<i32>, k: i32) -> i32 {
        let n = coins.len();
        // 构建邻接表（无向树）
        let mut children = vec![Vec::new(); n];
        for edge in edges {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            children[u].push(v);
            children[v].push(u);
        }

        // 记忆化数组：memo[node][f] 表示节点 node 已右移 f 次时的最大收益
        // f 的范围为 0..=13（因为 2^14=16384 足以覆盖大多数硬币值，且 f 不会超过 14）
        const MAX_SHIFT: usize = 14;
        let mut memo = vec![vec![-1; MAX_SHIFT]; n];

        // 深度优先搜索（递归闭包，捕获环境）
        fn dfs(
            node: usize,
            parent: usize,
            shift: usize,
            coins: &[i32],
            k: i32,
            children: &[Vec<usize>],
            memo: &mut [Vec<i32>],
        ) -> i32 {
            // 如果已计算过，直接返回
            if memo[node][shift] != -1 {
                return memo[node][shift];
            }

            // 当前节点的原始金币（考虑到已右移 shift 次）
            let val = coins[node] >> shift;

            // 选择1：不右移，获得 val - k，然后递归处理子节点（保持 shift 不变）
            let mut gain_no_shift = val - k;
            // 选择2：右移一次，获得 val >> 1，然后递归处理子节点（shift+1）
            let mut gain_shift = val >> 1;

            // 遍历所有邻居（子节点和父节点），跳过父节点
            for &child in &children[node] {
                if child == parent {
                    continue;
                }
                // 子节点在当前 shift 下的收益
                gain_no_shift += dfs(child, node, shift, coins, k, children, memo);
                // 子节点在 shift+1 下的收益（如果未超过限制）
                if shift + 1 < 14 {
                    gain_shift += dfs(child, node, shift + 1, coins, k, children, memo);
                }
            }

            // 取两种选择的最大值，并存入记忆化数组
            let best = gain_no_shift.max(gain_shift);
            memo[node][shift] = best;
            best
        }

        // 从根节点 0 开始，父节点设为 n（不可能的值），初始右移次数 0
        dfs(0, n, 0, &coins, k, &children, &mut memo)
    }
}
```
