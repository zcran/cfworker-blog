---
title: "leetcode-前缀和110"
date: 2026-08-25T10:02:53+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 最长特殊路径

给你一棵根节点为节点 0 的无向树，树中有 n 个节点，编号为 0 到 n - 1 ，这棵树通过一个长度为 n - 1 的二维数组 edges 表示，其中 edges[i] = [ui, vi, lengthi] 表示节点 ui 和 vi 之间有一条长度为 lengthi 的边。同时给你一个整数数组 nums ，其中 nums[i] 表示节点 i 的值。

特殊路径 指的是树中一条从祖先节点 往下 到后代节点且经过节点的值 互不相同 的路径。

注意 ，一条路径可以开始和结束于同一节点。

请你返回一个长度为 2 的数组 result ，其中 result[0] 是 最长 特殊路径的 长度 ，result[1] 是所有 最长特殊路径中的 最少 节点数目。


```
use std::collections::HashMap;

impl Solution {
    pub fn longest_special_path(edges: Vec<Vec<i32>>, nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();
        // 构建邻接表
        let mut graph = vec![Vec::new(); n];
        for e in edges {
            let (u, v, w) = (e[0] as usize, e[1] as usize, e[2] as i64);
            graph[u].push((v, w));
            graph[v].push((u, w));
        }

        // 答案：(最大长度, 最少节点数)，长度用 i64 避免溢出
        let mut ans = (-1i64, 0i32);
        // 前缀路径长度，dis[i] 表示从根到当前路径第 i 个节点的距离
        let mut dis = vec![0i64];
        // 颜色 -> 该颜色最近一次出现的深度索引（即 dis 中的位置）
        let mut last_pos = HashMap::new();

        // DFS 遍历树
        fn dfs(
            x: usize,
            fa: usize,
            top_depth: usize,
            nums: &[i32],
            graph: &[Vec<(usize, i64)>],
            dis: &mut Vec<i64>,
            last_pos: &mut HashMap<i32, usize>,
            ans: &mut (i64, i32),
        ) {
            let color = nums[x];
            let old_pos = last_pos.get(&color).copied().unwrap_or(0);
            // 当前路径中不能有重复颜色，top_depth 记录最近重复位置的下一个起点
            let top_depth = top_depth.max(old_pos);

            // 路径为 dis[top_depth..dis.len())，长度为 dis.last() - dis[top_depth]
            // 节点数为 dis.len() - top_depth
            let len = dis.last().unwrap() - dis[top_depth];
            let nodes = (dis.len() - top_depth) as i32;
            if len > ans.0 || (len == ans.0 && nodes < ans.1) {
                *ans = (len, nodes);
            }

            // 更新当前颜色的最近位置
            let new_pos = dis.len();
            last_pos.insert(color, new_pos);

            // 遍历邻接点
            for &(y, w) in &graph[x] {
                if y != fa {
                    dis.push(dis.last().unwrap() + w);
                    dfs(y, x, top_depth, nums, graph, dis, last_pos, ans);
                    dis.pop();
                }
            }

            // 恢复现场
            if old_pos == 0 {
                last_pos.remove(&color);
            } else {
                last_pos.insert(color, old_pos);
            }
        }

        dfs(0, usize::MAX, 0, &nums, &graph, &mut dis, &mut last_pos, &mut ans);

        vec![ans.0 as i32, ans.1]
    }
}
```
