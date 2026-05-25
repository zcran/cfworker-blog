---
title: "leetcode-拓扑排序1"
date: 2026-04-26T21:00:34+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 课程表

你这个学期必须选修 numCourses 门课程，记为 0 到 numCourses - 1 。

在选修某些课程之前需要一些先修课程。 先修课程按数组 prerequisites 给出，其中 prerequisites[i] = [ai, bi] ，表示如果要学习课程 ai 则 必须 先学习课程  bi 。

例如，先修课程对 [0, 1] 表示：想要学习课程 0 ，你需要先完成课程 1 。
请你判断是否可能完成所有课程的学习？如果可以，返回 true ；否则，返回 false 。


```
impl Solution {
    /// 判断是否能够完成所有课程的学习（即课程依赖图是否为有向无环图）
    ///
    /// # 参数
    /// - `num_courses`: 课程总数
    /// - `prerequisites`: 先修课程条件，每个元素 `[a, b]` 表示学习 `a` 前必须先学习 `b`
    ///
    /// # 返回
    /// - `true`: 可以完成所有课程
    /// - `false`: 存在循环依赖，无法完成
    ///
    /// # 算法
    /// 使用深度优先搜索（DFS）检测有向图中是否存在环：
    /// - 每个节点有三种状态：`Unvisited`（未访问）、`Visiting`（正在访问）、`Visited`（已完成）
    /// - 若在 `Visiting` 状态下再次遇到同一节点，则说明存在环
    /// - 遍历所有未访问节点，任一 DFS 过程发现环即返回 `false`
    pub fn can_finish(num_courses: i32, prerequisites: Vec<Vec<i32>>) -> bool {
        // 构建邻接表：graph[from] 存储所有依赖 from 的课程（即 from -> to 的边）
        let mut graph = vec![vec![]; num_courses as usize];
        for edge in prerequisites {
            let (to, from) = (edge[0] as usize, edge[1] as usize);
            graph[from].push(to);
        }

        /// 节点的访问状态
        #[derive(Clone, Copy, PartialEq)]
        enum State {
            Unvisited, // 尚未访问
            Visiting,  // 当前 DFS 路径中正在访问
            Visited,   // 已处理完毕且无环
        }

        /// 递归 DFS，返回 `true` 表示从当前节点出发无环，`false` 表示发现环
        fn dfs(node: usize, graph: &[Vec<usize>], state: &mut [State]) -> bool {
            use State::*;
            match state[node] {
                Visiting => false, // 遇到正在访问的节点 → 存在环
                Visited => true,   // 该节点已确认无环，直接返回
                Unvisited => {
                    // 标记为正在访问，并检查所有后继节点
                    state[node] = Visiting;
                    for &next in &graph[node] {
                        if !dfs(next, graph, state) {
                            return false; // 一旦发现环，立即向上传播
                        }
                    }
                    // 所有后继均无环，标记为已完成
                    state[node] = Visited;
                    true
                }
            }
        }

        let mut state = vec![State::Unvisited; num_courses as usize];
        for i in 0..num_courses as usize {
            if state[i] == State::Unvisited {
                if !dfs(i, &graph, &mut state) {
                    return false;
                }
            }
        }
        true
    }
}
```
