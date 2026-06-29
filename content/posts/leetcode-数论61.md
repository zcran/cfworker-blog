---
title: "leetcode-数论61"
date: 2026-06-20T11:09:57+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 完全平方数的祖先个数总和

给你一个整数 n，以及一棵以节点 0 为根、包含 n 个节点（编号从 0 到 n - 1）的无向树。该树由一个长度为 n - 1 的二维数组 edges 表示，其中 edges[i] = [ui, vi] 表示在节点 ui 与节点 vi 之间有一条无向边。

同时给你一个整数数组 nums，其中 nums[i] 是分配给节点 i 的正整数。

定义值 ti 为：节点 i 的 祖先 节点中，满足乘积 nums[i] * nums[ancestor] 为 完全平方数 的祖先个数。

请返回所有节点 i（范围为 [1, n - 1]）的 ti 之和。

说明：

在有根树中，节点 i 的祖先是指从节点 i 到根节点 0 的路径上、不包括 i 本身的所有节点。

完全平方数是可以表示为某个整数与其自身乘积的数，例如 1、4、9、16。


```
const MX: usize = 100_001;

lazy_static::lazy_static! {
    /// core[n] 表示 n 的"平方自由核"：将 n 中的所有平方因子移除后剩下的数
    /// 例如：core[12] = 3 (因为 12 = 2² * 3，移除 2² 后为 3)
    /// 两个数乘积为完全平方数 ⇔ 它们的平方自由核相同
    static ref CORE: Vec<usize> = {
        let mut core = vec![0; MX];
        for i in 1..MX {
            if core[i] == 0 { // i 是平方自由数
                // 标记所有 i * j² 的核心为 i
                for j in 1.. {
                    let idx = i * j * j;
                    if idx >= MX { break; }
                    core[idx] = i;
                }
            }
        }
        core
    };
}

impl Solution {
    /// 计算所有非根节点的祖先中，与当前节点乘积为完全平方数的数量之和
    pub fn sum_of_ancestors(n: i32, edges: Vec<Vec<i32>>, nums: Vec<i32>) -> i64 {
        let n = n as usize;

        // 构建邻接表
        let mut graph = vec![vec![]; n];
        for edge in &edges {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            graph[u].push(v);
            graph[v].push(u);
        }

        // 统计当前路径上各平方自由核的出现次数
        use std::collections::HashMap;
        let mut freq = HashMap::new();
        let mut ans = 0i64;

        // DFS：x 当前节点，parent 父节点
        fn dfs(
            x: usize,
            parent: usize,
            graph: &Vec<Vec<usize>>,
            nums: &Vec<i32>,
            core: &Vec<usize>,
            freq: &mut HashMap<usize, i32>,
            ans: &mut i64,
        ) {
            let key = core[nums[x] as usize];

            // 当前节点的祖先（不包括自身）中与 nums[x] 乘积为完全平方数的个数
            *ans += *freq.get(&key).unwrap_or(&0) as i64;

            // 将当前节点加入路径统计
            *freq.entry(key).or_insert(0) += 1;

            // 递归遍历子节点
            for &next in &graph[x] {
                if next != parent {
                    dfs(next, x, graph, nums, core, freq, ans);
                }
            }

            // 恢复现场：回溯时移除当前节点的贡献
            *freq.get_mut(&key).unwrap() -= 1;
            if freq[&key] == 0 {
                freq.remove(&key);
            }
        }

        dfs(0, n, &graph, &nums, &CORE, &mut freq, &mut ans);
        ans
    }
}
```
