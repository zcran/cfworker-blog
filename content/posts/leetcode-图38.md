---
title: "leetcode-图38"
date: 2026-07-27T10:01:37+08:00
tags: ["leetcode", "图"]
draft: false
---


## 移除可疑的方法

你正在维护一个项目，该项目有 n 个方法，编号从 0 到 n - 1。

给你两个整数 n 和 k，以及一个二维整数数组 invocations，其中 invocations[i] = [ai, bi] 表示方法 ai 调用了方法 bi。

已知如果方法 k 存在一个已知的 bug。那么方法 k 以及它直接或间接调用的任何方法都被视为 可疑方法 ，我们需要从项目中移除这些方法。

只有当一组方法没有被这组之外的任何方法调用时，这组方法才能被移除。

返回一个数组，包含移除所有 可疑方法 后剩下的所有方法。你可以以任意顺序返回答案。如果无法移除 所有 可疑方法，则 不 移除任何方法。


```
use std::collections::VecDeque;

impl Solution {
    pub fn remaining_methods(n: i32, k: i32, invocations: Vec<Vec<i32>>) -> Vec<i32> {
        let n = n as usize;
        let k = k as usize;

        // 构建调用图
        let mut graph = vec![Vec::new(); n];
        let mut indegree = vec![0; n];
        for inv in invocations {
            let (u, v) = (inv[0] as usize, inv[1] as usize);
            graph[u].push(v);
            indegree[v] += 1;
        }

        // 标记所有可疑方法（从 k 可达）
        let mut suspicious = vec![false; n];
        let mut queue = VecDeque::new();
        suspicious[k] = true;
        queue.push_back(k);

        while let Some(u) = queue.pop_front() {
            for &v in &graph[u] {
                if !suspicious[v] {
                    suspicious[v] = true;
                    queue.push_back(v);
                }
            }
        }

        // 检查是否有外部方法调用了可疑方法
        // 如果有，则无法移除任何方法
        for u in 0..n {
            if !suspicious[u] {
                for &v in &graph[u] {
                    if suspicious[v] {
                        // 非可疑方法调用了可疑方法，无法移除
                        return (0..n as i32).collect();
                    }
                }
            }
        }

        // 可以移除所有可疑方法
        (0..n as i32)
            .filter(|&i| !suspicious[i as usize])
            .collect()
    }
}
```
