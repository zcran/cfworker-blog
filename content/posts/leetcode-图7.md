---
title: "leetcode-图7"
date: 2026-07-27T10:01:35+08:00
tags: ["leetcode", "图"]
draft: false
---


## 不邻接植花

有 n 个花园，按从 1 到 n 标记。另有数组 paths ，其中 paths[i] = [xi, yi] 描述了花园 xi 到花园 yi 的双向路径。在每个花园中，你打算种下四种花之一。

另外，所有花园 最多 有 3 条路径可以进入或离开.

你需要为每个花园选择一种花，使得通过路径相连的任何两个花园中的花的种类互不相同。

以数组形式返回 任一 可行的方案作为答案 answer，其中 answer[i] 为在第 (i+1) 个花园中种植的花的种类。花的种类用  1、2、3、4 表示。保证存在答案。


```
impl Solution {
    pub fn garden_no_adj(n: i32, paths: Vec<Vec<i32>>) -> Vec<i32> {
        let n = n as usize;

        // 构建邻接表
        let mut graph = vec![vec![]; n];
        for p in &paths {
            let (u, v) = (p[0] as usize - 1, p[1] as usize - 1);
            graph[u].push(v);
            graph[v].push(u);
        }

        let mut ans = vec![0; n];

        // 贪心着色：每个花园选择相邻未使用的最小颜色
        for i in 0..n {
            let mut used = [false; 5];  // 索引 1..4 表示颜色是否被使用

            for &neighbor in &graph[i] {
                if ans[neighbor] != 0 {
                    used[ans[neighbor] as usize] = true;
                }
            }

            // 选择最小的可用颜色
            for color in 1..=4 {
                if !used[color] {
                    ans[i] = color as i32;  // 转换为 i32 类型
                    break;
                }
            }
        }

        ans
    }
}
```
