---
title: "leetcode-树3"
date: 2026-09-24T09:47:33+08:00
tags: ["leetcode", "树"]
draft: false
---


## 统计可能的树根数目

Alice 有一棵 n 个节点的树，节点编号为 0 到 n - 1 。树用一个长度为 n - 1 的二维整数数组 edges 表示，其中 edges[i] = [ai, bi] ，表示树中节点 ai 和 bi 之间有一条边。

Alice 想要 Bob 找到这棵树的根。她允许 Bob 对这棵树进行若干次 猜测 。每一次猜测，Bob 做如下事情：

· 选择两个 不相等 的整数 u 和 v ，且树中必须存在边 [u, v] 。
· Bob 猜测树中 u 是 v 的 父节点 。

Bob 的猜测用二维整数数组 guesses 表示，其中 guesses[j] = [uj, vj] 表示 Bob 猜 uj 是 vj 的父节点。

Alice 非常懒，她不想逐个回答 Bob 的猜测，只告诉 Bob 这些猜测里面 至少 有 k 个猜测的结果为 true 。

给你二维整数数组 edges ，Bob 的所有猜测和整数 k ，请你返回可能成为树根的 节点数目 。如果没有这样的树，则返回 0。




```
use std::collections::HashSet;

impl Solution {
    pub fn root_count(edges: Vec<Vec<i32>>, guesses: Vec<Vec<i32>>, k: i32) -> i32 {
        let n = edges.len() + 1;

        // 建邻接表
        let mut graph = vec![Vec::new(); n];
        for edge in &edges {
            let (x, y) = (edge[0] as usize, edge[1] as usize);
            graph[x].push(y);
            graph[y].push(x);
        }

        // 将 guesses 中的有向边编码为 u64，便于 O(1) 查询
        // 编码方式：高 32 位存 u，低 32 位存 v
        let mut guess_set = HashSet::with_capacity(guesses.len());
        for g in &guesses {
            let (u, v) = (g[0] as u64, g[1] as u64);
            guess_set.insert((u << 32) | v);
        }

        #[inline]
        fn key(u: usize, v: usize) -> u64 {
            ((u as u64) << 32) | (v as u64)
        }

        // 第一次 DFS：以 0 为根，计算根为 0 时满足的猜测数
        fn dfs(
            x: usize,
            parent: usize,
            graph: &[Vec<usize>],
            guess_set: &HashSet<u64>,
        ) -> i32 {
            let mut cnt = 0;
            for &y in &graph[x] {
                if y == parent {
                    continue;
                }
                // 若猜测 x 是 y 的父节点，则命中
                if guess_set.contains(&key(x, y)) {
                    cnt += 1;
                }
                cnt += dfs(y, x, graph, guess_set);
            }
            cnt
        }

        // 第二次 DFS：换根 DP，从当前根 x 转移到子节点 y
        fn reroot(
            x: usize,
            parent: usize,
            cnt: i32,
            k: i32,
            graph: &[Vec<usize>],
            guess_set: &HashSet<u64>,
            ans: &mut i32,
        ) {
            if cnt >= k {
                *ans += 1;
            }
            for &y in &graph[x] {
                if y == parent {
                    continue;
                }
                // 换根：x -> y
                // 原来以 x 为根时，若猜测 (x, y) 命中，换根后不再命中，减 1
                // 若猜测 (y, x) 命中，换根后变为命中，加 1
                let mut new_cnt = cnt;
                if guess_set.contains(&key(x, y)) {
                    new_cnt -= 1;
                }
                if guess_set.contains(&key(y, x)) {
                    new_cnt += 1;
                }
                reroot(y, x, new_cnt, k, graph, guess_set, ans);
            }
        }

        // 以 0 为根时的命中数
        let cnt0 = dfs(0, usize::MAX, &graph, &guess_set);

        let mut ans = 0;
        reroot(0, usize::MAX, cnt0, k, &graph, &guess_set, &mut ans);
        ans
    }
}
```
