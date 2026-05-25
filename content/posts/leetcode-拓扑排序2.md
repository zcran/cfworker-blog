---
title: "leetcode-拓扑排序2"
date: 2026-04-26T21:00:34+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 课程表 II


现在你总共有 numCourses 门课需要选，记为 0 到 numCourses - 1。给你一个数组 prerequisites ，其中 prerequisites[i] = [ai, bi] ，表示在选修课程 ai 前 必须 先选修 bi 。

例如，想要学习课程 0 ，你需要先完成课程 1 ，我们用一个匹配来表示：[0,1] 。
返回你为了学完所有课程所安排的学习顺序。可能会有多个正确的顺序，你只要返回 任意一种 就可以了。如果不可能完成所有课程，返回 一个空数组 。


```
use std::collections::VecDeque;

impl Solution {
    /// 返回课程的学习顺序（拓扑排序），若存在循环依赖则返回空向量。
    ///
    /// # 参数
    /// - `num_courses`: 课程总数，编号从 `0` 到 `num_courses - 1`
    /// - `prerequisites`: 先修条件，每个元素 `[a, b]` 表示「学习 `a` 前必须先学习 `b`」
    ///
    /// # 返回值
    /// - 若所有课程均可完成，返回一种可行的学习顺序（BFS 拓扑序）
    /// - 若存在环（无法完成全部课程），返回空向量 `vec![]`
    ///
    /// # 算法描述
    /// Kahn 算法（基于 BFS）：
    /// 1. 构建邻接表（`graph`）和入度数组（`indegree`）
    /// 2. 将所有入度为 0 的课程入队
    /// 3. 循环取出队首节点，将其加入答案序列，并将其所有后继节点的入度减 1
    ///    - 若某后继入度变为 0，则将其入队
    /// 4. 若最终答案长度等于课程总数，则返回答案；否则存在环，返回空向量
    pub fn find_order(num_courses: i32, prerequisites: Vec<Vec<i32>>) -> Vec<i32> {
        let n = num_courses as usize;

        // ---------- 1. 构建图 ----------
        // 邻接表：graph[from] 存储所有依赖 from 的课程（即 from → to）
        let mut graph = vec![vec![]; n];
        // 入度数组：indegree[course] 表示该课程未完成的先修课程数量
        let mut indegree = vec![0; n];

        for edge in prerequisites {
            // edge[0] = 课程 a , edge[1] = 课程 b
            let (to, from) = (edge[0] as usize, edge[1] as usize);
            graph[from].push(to);
            indegree[to] += 1;
        }

        // ---------- 2. 初始化 BFS 队列 ----------
        // 函数式风格：通过 filter 收集所有入度为 0 的节点
        let mut queue: VecDeque<usize> = (0..n)
            .filter(|&i| indegree[i] == 0)
            .collect();

        // ---------- 3. 执行拓扑排序 ----------
        let mut order = Vec::with_capacity(n);
        while let Some(u) = queue.pop_front() {
            order.push(u as i32);           // 学习课程 u
            for &v in &graph[u] {
                indegree[v] -= 1;           // 移除 u 对 v 的限制
                if indegree[v] == 0 {
                    queue.push_back(v);     // v 所有前置课程均已学完
                }
            }
        }

        // ---------- 4. 判断是否有环 ----------
        if order.len() == n { order } else { Vec::new() }
    }
}
```
