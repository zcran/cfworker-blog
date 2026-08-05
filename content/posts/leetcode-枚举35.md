---
title: "leetcode-枚举35"
date: 2026-07-09T10:05:01+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 创建价值相同的连通块

有一棵 n 个节点的无向树，节点编号为 0 到 n - 1 。

给你一个长度为 n 下标从 0 开始的整数数组 nums ，其中 nums[i] 表示第 i 个节点的值。同时给你一个长度为 n - 1 的二维整数数组 edges ，其中 edges[i] = [ai, bi] 表示节点 ai 与 bi 之间有一条边。

你可以 删除 一些边，将这棵树分成几个连通块。一个连通块的 价值 定义为这个连通块中 所有 节点 i 对应的 nums[i] 之和。

你需要删除一些边，删除后得到的各个连通块的价值都相等。请返回你可以删除的边数 最多 为多少。


```
impl Solution {
    pub fn component_value(nums: Vec<i32>, edges: Vec<Vec<i32>>) -> i32 {
        let n = nums.len();
        let total: i32 = nums.iter().sum();
        let max_val = *nums.iter().max().unwrap();

        // 构建邻接表
        let mut graph = vec![vec![]; n];
        for e in edges {
            let u = e[0] as usize;
            let v = e[1] as usize;
            graph[u].push(v);
            graph[v].push(u);
        }

        // 检查是否可以将树划分为若干连通块，每块和为 target
        fn can_partition(graph: &[Vec<usize>], nums: &[i32], target: i32) -> bool {
            // DFS 返回当前子树的和，如果无法划分则返回 -1
            fn dfs(
                graph: &[Vec<usize>],
                nums: &[i32],
                target: i32,
                x: usize,
                parent: usize,
            ) -> i32 {
                let mut sum = nums[x];

                for &y in &graph[x] {
                    if y == parent {
                        continue;
                    }

                    let sub_sum = dfs(graph, nums, target, y, x);
                    if sub_sum == -1 {
                        return -1;
                    }
                    sum += sub_sum;
                }

                // 如果当前子树和大于 target，无法划分
                if sum > target {
                    return -1;
                }

                // 如果和等于 target，则切掉这个连通块，向上返回 0
                if sum == target {
                    return 0;
                }

                sum // 否则返回当前和继续向上合并
            }

            dfs(graph, nums, target, 0, usize::MAX) == 0
        }

        // 最多能分成 total / max_val 块
        let max_parts = total / max_val;

        // 从大到小尝试划分成 i 块，每块和为 target
        for parts in (1..=max_parts).rev() {
            if total % parts == 0 {
                let target = total / parts;
                if can_partition(&graph, &nums, target) {
                    return parts - 1; // 删除的边数 = 块数 - 1
                }
            }
        }

        0
    }
}
```
