---
title: "leetcode-有序集合35"
date: 2026-06-14T10:47:42+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 第 K 小的路径异或和


给定一棵以节点 0 为根的无向树，带有 n 个节点，按 0 到 n - 1 编号。每个节点 i 有一个整数值 vals[i]，并且它的父节点通过 par[i] 给出。

从根节点 0 到节点 u 的 路径异或和 定义为从根节点到节点 u 的路径上所有节点 i 的 vals[i] 的按位异或，包括节点 u。

给定一个 2 维整数数组 queries，其中 queries[j] = [uj, kj]。对于每个查询，找到以 uj 为根的子树的所有节点中，第 kj 小 的 不同 路径异或和。如果子树中 不同 的异或路径和少于 kj，答案为 -1。

返回一个整数数组，其中第 j 个元素是第 j 个查询的答案。

在有根树中，节点 v 的子树包括 v 以及所有经过 v 到达根节点路径上的节点，即 v 及其后代节点。

```
use std::cell::RefCell;
use std::collections::HashMap;
use std::rc::Rc;

impl Solution {
    pub fn kth_smallest(par: Vec<i32>, vals: Vec<i32>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let n = par.len();
        // 建树: 邻接表
        let mut children = vec![Vec::new(); n];
        for i in 1..n {
            children[par[i] as usize].push(i);
        }

        // DFS1: 计算每个节点的根路径异或值
        let mut path_xor = vec![0; n];
        Self::dfs1(0, 0, &vals, &children, &mut path_xor);

        // 确定值域范围 (路径异或值的最大值)
        let max_val = *path_xor.iter().max().unwrap() as usize;

        // 按节点分组查询: (k, 查询索引)
        let mut queries_by_node: Vec<Vec<(usize, usize)>> = vec![Vec::new(); n];
        for (idx, q) in queries.iter().enumerate() {
            let u = q[0] as usize;
            let k = q[1] as usize;
            queries_by_node[u].push((k, idx));
        }

        let mut answers = vec![-1; queries.len()];

        // 使用可持久化线段树的变体: 每个节点维护一棵子树内路径值的集合
        // 这里使用动态开点权值线段树 + 启发式合并
        // 节点索引: 0 表示空节点
        let mut seg = SegmentTree::new(max_val);

        // DFS2: 后序遍历合并子树
        Self::dfs2(
            0,
            &children,
            &path_xor,
            &queries_by_node,
            &mut answers,
            &mut seg,
        );

        answers
    }

    // DFS 计算路径异或值
    fn dfs1(u: usize, current: i32, vals: &[i32], children: &[Vec<usize>], path_xor: &mut [i32]) {
        let val = current ^ vals[u];
        path_xor[u] = val;
        for &v in &children[u] {
            Self::dfs1(v, val, vals, children, path_xor);
        }
    }

    // DFS 后序遍历，合并子树线段树，回答查询
    fn dfs2(
        u: usize,
        children: &[Vec<usize>],
        path_xor: &[i32],
        queries_by_node: &[Vec<(usize, usize)>],
        answers: &mut [i32],
        seg: &mut SegmentTree,
    ) -> usize {
        // 创建包含当前节点值的线段树
        let mut root = seg.insert(0, 0, seg.max_val, path_xor[u] as usize);

        // 合并所有子节点的线段树
        for &v in &children[u] {
            let child_root = Self::dfs2(v, children, path_xor, queries_by_node, answers, seg);
            root = seg.merge(root, child_root, 0, seg.max_val);
        }

        // 回答以 u 为根的查询
        for &(k, idx) in &queries_by_node[u] {
            answers[idx] = seg.kth(root, 0, seg.max_val, k) as i32;
        }

        root
    }
}

// 动态开点权值线段树
struct SegmentTree {
    // 线段树节点: [左子节点, 右子节点, 区间内不同值的个数]
    left: Vec<usize>,
    right: Vec<usize>,
    count: Vec<i32>,
    max_val: usize,
}

impl SegmentTree {
    fn new(max_val: usize) -> Self {
        // 初始化空节点 0
        SegmentTree {
            left: vec![0],
            right: vec![0],
            count: vec![0],
            max_val,
        }
    }

    // 新建节点
    fn new_node(&mut self) -> usize {
        let idx = self.left.len();
        self.left.push(0);
        self.right.push(0);
        self.count.push(0);
        idx
    }

    // 插入值 val (确保重复值只计一次)
    fn insert(&mut self, node: usize, l: usize, r: usize, val: usize) -> usize {
        // 如果当前节点为空，新建
        let mut cur = if node == 0 {
            self.new_node()
        } else {
            node
        };

        if l == r {
            // 叶子节点: 标记存在
            self.count[cur] = 1;
            return cur;
        }

        let mid = (l + r) >> 1;
        if val <= mid {
            let child = self.insert(self.left[cur], l, mid, val);
            self.left[cur] = child;
        } else {
            let child = self.insert(self.right[cur], mid + 1, r, val);
            self.right[cur] = child;
        }

        // 更新计数
        self.count[cur] = self.count[self.left[cur]] + self.count[self.right[cur]];
        cur
    }

    // 合并两棵线段树 (启发式合并, 但这里直接合并)
    fn merge(&mut self, node1: usize, node2: usize, l: usize, r: usize) -> usize {
        if node1 == 0 {
            return node2;
        }
        if node2 == 0 {
            return node1;
        }

        if l == r {
            // 叶子节点: 只要存在就保留
            self.count[node1] = 1;
            return node1;
        }

        let mid = (l + r) >> 1;
        let left_merged = self.merge(self.left[node1], self.left[node2], l, mid);
        let right_merged = self.merge(self.right[node1], self.right[node2], mid + 1, r);

        self.left[node1] = left_merged;
        self.right[node1] = right_merged;
        self.count[node1] = self.count[left_merged] + self.count[right_merged];

        node1
    }

    // 查询第 k 小 (k 从 1 开始)
    fn kth(&self, node: usize, l: usize, r: usize, k: usize) -> i32 {
        if node == 0 || self.count[node] < k as i32 {
            return -1;
        }

        if l == r {
            return l as i32;
        }

        let mid = (l + r) >> 1;
        let left_count = self.count[self.left[node]] as usize;

        if left_count >= k {
            self.kth(self.left[node], l, mid, k)
        } else {
            self.kth(self.right[node], mid + 1, r, k - left_count)
        }
    }
}
```
