---
title: "leetcode-堆57"
date: 2026-08-21T09:54:14+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 树中每个节点放置的金币数目

给你一棵 n 个节点的 无向 树，节点编号为 0 到 n - 1 ，树的根节点在节点 0 处。同时给你一个长度为 n - 1 的二维整数数组 edges ，其中 edges[i] = [aˇi, bˇi] 表示树中节点 aˇi 和 bˇi 之间有一条边。

给你一个长度为 n 下标从 0 开始的整数数组 cost ，其中 cost[i] 是第 i 个节点的 开销 。

你需要在树中每个节点都放置金币，在节点 i 处的金币数目计算方法如下：

如果节点 i 对应的子树中的节点数目小于 3 ，那么放 1 个金币。

否则，计算节点 i 对应的子树内 3 个不同节点的开销乘积的 最大值 ，并在节点 i 处放置对应数目的金币。如果最大乘积是 负数 ，那么放置 0 个金币。

请你返回一个长度为 n 的数组 coin ，coin[i]是节点 i 处的金币数目。


```
impl Solution {
    pub fn placed_coins(edges: Vec<Vec<i32>>, cost: Vec<i32>) -> Vec<i64> {
        let n = cost.len();
        let mut g = vec![vec![]; n];
        for e in &edges {
            let (x, y) = (e[0] as usize, e[1] as usize);
            g[x].push(y);
            g[y].push(x);
        }

        let mut ans = vec![1i64; n];

        // DFS 返回子树中保留的关键值：最多 5 个 [最小2个, ..., 最大3个]
        fn dfs(x: usize, fa: usize, g: &Vec<Vec<usize>>, cost: &Vec<i32>, ans: &mut Vec<i64>) -> Vec<i64> {
            let mut vals = vec![cost[x] as i64];

            for &y in &g[x] {
                if y != fa {
                    vals.extend(dfs(y, x, g, cost, ans));
                }
            }

            vals.sort_unstable();
            let m = vals.len();

            // 子树节点数 >= 3 时，计算最大乘积
            if m >= 3 {
                let max3 = vals[m - 1] * vals[m - 2] * vals[m - 3];
                let min2_max1 = vals[0] * vals[1] * vals[m - 1];
                ans[x] = max3.max(min2_max1).max(0);
            }

            // 剪枝：只保留最多 5 个关键值传给父节点
            if m > 5 {
                vals = vec![vals[0], vals[1], vals[m - 3], vals[m - 2], vals[m - 1]];
            }
            vals
        }

        dfs(0, n, &g, &cost, &mut ans);
        ans
    }
}
```
