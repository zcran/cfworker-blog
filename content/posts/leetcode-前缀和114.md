---
title: "leetcode-前缀和114"
date: 2026-08-25T10:02:53+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 最长特殊路径 II

给你一棵无向树，根节点为 0，树有 n 个节点，节点编号从 0 到 n - 1。这个树由一个长度为 n - 1 的二维数组 edges 表示，其中 edges[i] = [ui, vi, lengthi] 表示节点 ui 和 vi 之间有一条长度为 lengthi 的边。同时给你一个整数数组 nums，其中 nums[i] 表示节点 i 的值。

一条 特殊路径 定义为一个从祖先节点到子孙节点的 向下 路径，路径中所有节点值都是唯一的，最多允许有一个值出现两次。

返回一个大小为 2 的数组 result，其中 result[0] 是 最长 特殊路径的 长度 ，result[1] 是所有 最长 特殊路径中的 最少 节点数。




```
use std::collections::HashMap;

impl Solution {
    pub fn longest_special_path(edges: Vec<Vec<i32>>, nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();
        // 构建邻接表
        let mut g = vec![Vec::new(); n];
        for e in edges {
            let (u, v, w) = (e[0] as usize, e[1] as usize, e[2] as i64);
            g[u].push((v, w));
            g[v].push((u, w));
        }

        // 答案：(最大长度, 最少节点数)
        let mut ans = (-1i64, 0i32);
        // 前缀距离，dis[i] 表示从根到第 i 个节点的路径长度
        let mut dis = vec![0i64];
        // 颜色 -> 该颜色最近一次出现的位置（即该节点在 dis 中的索引）
        let mut last_depth = HashMap::new();

        fn dfs(
            x: usize,
            fa: usize,
            mut top_depth: usize, // 当前窗口左端点（索引）
            last1: usize,         // 当前窗口内已存在的重复颜色的第一次出现位置
            nums: &[i32],
            g: &[Vec<(usize, i64)>],
            dis: &mut Vec<i64>,
            last_depth: &mut HashMap<i32, usize>,
            ans: &mut (i64, i32),
        ) {
            let color = nums[x];
            let last2 = *last_depth.get(&color).unwrap_or(&0);

            // 更新窗口左端点：最多允许一个重复，取两个重复位置中较前的那个
            top_depth = top_depth.max(last1.min(last2));

            // 计算当前路径（窗口）的长度和节点数
            let len = dis.last().unwrap() - dis[top_depth];
            let nodes = (dis.len() - top_depth) as i32;
            if len > ans.0 || (len == ans.0 && nodes < ans.1) {
                *ans = (len, nodes);
            }

            // 记录当前颜色位置
            let cur_pos = dis.len();
            last_depth.insert(color, cur_pos);

            // 传递给子节点的新 last1
            let new_last1 = last1.max(last2);

            // 遍历子节点
            for &(y, w) in &g[x] {
                if y != fa {
                    dis.push(dis.last().unwrap() + w);
                    dfs(y, x, top_depth, new_last1, nums, g, dis, last_depth, ans);
                    dis.pop();
                }
            }

            // 恢复现场
            if last2 == 0 {
                last_depth.remove(&color);
            } else {
                last_depth.insert(color, last2);
            }
        }

        dfs(0, usize::MAX, 0, 0, &nums, &g, &mut dis, &mut last_depth, &mut ans);
        vec![ans.0 as i32, ans.1]
    }
}
```
