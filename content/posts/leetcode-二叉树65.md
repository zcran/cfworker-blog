---
title: "leetcode-二叉树65"
date: 2026-08-15T11:32:31+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 统计最高分的节点数目

给你一棵根节点为 0 的 二叉树 ，它总共有 n 个节点，节点编号为 0 到 n - 1 。同时给你一个下标从 0 开始的整数数组 parents 表示这棵树，其中 parents[i] 是节点 i 的父节点。由于节点 0 是根，所以 parents[0] == -1 。

一个子树的 大小 为这个子树内节点的数目。每个节点都有一个与之关联的 分数 。求出某个节点分数的方法是，将这个节点和与它相连的边全部 删除 ，剩余部分是若干个 非空 子树，这个节点的 分数 为所有这些子树 大小的乘积 。

请你返回有 最高得分 节点的 数目 。


```
impl Solution {
    pub fn count_highest_score_nodes(parents: Vec<i32>) -> i32 {
        let n = parents.len();
        let mut children = vec![Vec::new(); n];
        for i in 1..n {
            children[parents[i] as usize].push(i);
        }

        let mut scores = vec![0i64; n];
        Self::dfs(&children, &mut scores, 0, n as i64);

        let max = *scores.iter().max().unwrap();
        scores.iter().filter(|&&s| s == max).count() as i32
    }

    /// 返回以 u 为根的子树大小，同时计算 u 的分数存入 scores[u]
    /// 分数 = 各子树大小的乘积 × 上方剩余部分大小（根节点无上方部分，视为 1）
    fn dfs(children: &[Vec<usize>], scores: &mut [i64], u: usize, n: i64) -> i64 {
        let mut prod = 1i64;
        let mut subtree_sum = 1i64; // 包含自身

        for &v in &children[u] {
            let sz = Self::dfs(children, scores, v, n);
            prod *= sz;
            subtree_sum += sz;
        }

        // 上方部分 = 总节点数 - 当前子树大小，根节点上方为 0，但乘积中需视为 1
        let up = (n - subtree_sum).max(1);
        scores[u] = prod * up;
        subtree_sum
    }
}
```
