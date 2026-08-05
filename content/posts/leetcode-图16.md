---
title: "leetcode-图16"
date: 2026-07-27T10:01:36+08:00
tags: ["leetcode", "图"]
draft: false
---


## 重构一棵树的方案数

给你一个数组 pairs ，其中 pairs[i] = [xi, yi] ，并且满足：

· pairs 中没有重复元素
· xi < yi

令 ways 为满足下面条件的有根树的方案数：

· 树所包含的所有节点值都在 pairs 中。
· 一个数对 [xi, yi] 出现在 pairs 中 当且仅当 xi 是 yi 的祖先或者 yi 是 xi 的祖先。
· 注意：构造出来的树不一定是二叉树。

两棵树被视为不同的方案当存在至少一个节点在两棵树中有不同的父节点。

请你返回：

· 如果 ways == 0 ，返回 0 。
· 如果 ways == 1 ，返回 1 。
· 如果 ways > 1 ，返回 2 。

一棵 有根树 指的是只有一个根节点的树，所有边都是从根往外的方向。

我们称从根到一个节点路径上的任意一个节点（除去节点本身）都是该节点的 祖先 。根节点没有祖先。


```
use std::collections::{HashMap, HashSet};

impl Solution {
    pub fn check_ways(pairs: Vec<Vec<i32>>) -> i32 {
        // 构建邻接表（无向图）
        let mut graph: HashMap<i32, HashSet<i32>> = HashMap::new();
        for p in pairs {
            graph.entry(p[0]).or_default().insert(p[1]);
            graph.entry(p[1]).or_default().insert(p[0]);
        }

        // 按度数升序排列节点
        let mut nodes: Vec<i32> = graph.keys().copied().collect();
        nodes.sort_by_key(|&x| graph[&x].len());

        // 构建可能的树结构
        let mut parent_map: HashMap<i32, Vec<i32>> = HashMap::new();
        let mut root = -1;
        let mut ways = 1;

        for i in 0..nodes.len() {
            let child = nodes[i];
            // 找到度数最小且包含 child 的祖先
            let mut parent_idx = i + 1;
            while parent_idx < nodes.len() && !graph[&nodes[parent_idx]].contains(&child) {
                parent_idx += 1;
            }

            if parent_idx < nodes.len() {
                let parent = nodes[parent_idx];
                parent_map.entry(parent).or_default().push(child);
                // 如果度相同，存在多种构造方式
                if graph[&parent].len() == graph[&child].len() {
                    ways = 2;
                }
            } else {
                // 没有祖先的节点作为根
                if root == -1 {
                    root = child;
                } else {
                    return 0; // 多个根，无法构造
                }
            }
        }

        // DFS 验证祖先关系
        fn validate(
            node: i32,
            depth: i32,
            graph: &HashMap<i32, HashSet<i32>>,
            parent_map: &HashMap<i32, Vec<i32>>,
            visited: &mut HashSet<i32>,
        ) -> (i32, bool) {
            if visited.contains(&node) {
                return (0, false);
            }
            visited.insert(node);

            let mut subtree_size = 1;
            if let Some(children) = parent_map.get(&node) {
                for &child in children {
                    let (size, ok) = validate(child, depth + 1, graph, parent_map, visited);
                    if !ok {
                        return (0, false);
                    }
                    subtree_size += size;
                }
            }

            // 祖先数量 = 度数 - 子树大小 + 1（自身）
            // 验证：节点的祖先数 = 图中邻接数 - 子树大小
            let expected_ancestors = graph[&node].len() as i32 - (subtree_size - 1);
            let actual_ancestors = depth as i32;
            if actual_ancestors != expected_ancestors {
                return (0, false);
            }

            (subtree_size, true)
        }

        let mut visited = HashSet::new();
        let (_, valid) = validate(root, 0, &graph, &parent_map, &mut visited);

        if !valid || visited.len() != graph.len() {
            return 0;
        }

        ways
    }
}
```
