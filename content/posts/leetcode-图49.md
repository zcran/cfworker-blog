---
title: "leetcode-图49"
date: 2026-08-01T09:50:11+08:00
tags: ["leetcode", "图"]
draft: false
---


## 传递信息

小朋友 A 在和 ta 的小伙伴们玩传信息游戏，游戏规则如下：

1.有 n 名玩家，所有玩家编号分别为 0 ～ n-1，其中小朋友 A 的编号为 0
2.每个玩家都有固定的若干个可传信息的其他玩家（也可能没有）。传信息的关系是单向的（比如 A 可以向 B 传信息，但 B 不能向 A 传信息）。
3.每轮信息必须需要传递给另一个人，且信息可重复经过同一个人

给定总玩家数 n，以及按 [玩家编号,对应可传递玩家编号] 关系组成的二维数组 relation。返回信息从小 A (编号 0 ) 经过 k 轮传递到编号为 n-1 的小伙伴处的方案数；若不能到达，返回 0。


```
impl Solution {
    /// 计算从玩家 0 经过 k 轮传递到达玩家 n-1 的方案数
    ///
    /// # 参数
    /// - `n`: 玩家总数
    /// - `relation`: 传递关系，relation[i] = [u, v] 表示 u 可传递给 v
    /// - `k`: 传递轮数
    ///
    /// # 返回
    /// 方案数，若无法到达返回 0
    ///
    /// # 算法
    /// 使用动态规划：dp[step][node] 表示经过 step 轮到达 node 的方案数
    /// 时间复杂度 O(k * m)，空间复杂度 O(n)
    pub fn num_ways(n: i32, relation: Vec<Vec<i32>>, k: i32) -> i32 {
        let n = n as usize;
        let k = k as usize;

        // 构建邻接表（使用 Vec 代替 HashMap，更快更省内存）
        let mut graph = vec![Vec::new(); n];
        for rel in relation {
            let u = rel[0] as usize;
            let v = rel[1] as usize;
            graph[u].push(v);
        }

        // dp[j] 表示当前轮到达节点 j 的方案数
        let mut dp = vec![0; n];
        dp[0] = 1; // 第 0 轮在起点

        // 迭代 k 轮
        for _ in 0..k {
            let mut next = vec![0; n];
            // 遍历所有节点
            for u in 0..n {
                if dp[u] == 0 {
                    continue;
                }
                // 从 u 传递到所有可达节点
                for &v in &graph[u] {
                    next[v] += dp[u];
                }
            }
            dp = next;
        }

        dp[n - 1]
    }
}
```
