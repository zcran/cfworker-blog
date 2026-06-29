---
title: "leetcode-并查集30"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 二维网格图中探测环

给你一个二维字符网格数组 grid ，大小为 m x n ，你需要检查 grid 中是否存在 相同值 形成的环。

一个环是一条开始和结束于同一个格子的长度 大于等于 4 的路径。对于一个给定的格子，你可以移动到它上、下、左、右四个方向相邻的格子之一，可以移动的前提是这两个格子有 相同的值 。

同时，你也不能回到上一次移动时所在的格子。比方说，环  (1, 1) -> (1, 2) -> (1, 1) 是不合法的，因为从 (1, 2) 移动到 (1, 1) 回到了上一次移动时的格子。

如果 grid 中有相同值形成的环，请你返回 true ，否则返回 false 。


```
impl Solution {
    /// 检测网格中是否存在由相同字符形成的环。
    /// 使用并查集检查连通性，若连接两个已连通节点则存在环。
    pub fn contains_cycle(grid: Vec<Vec<char>>) -> bool {
        let m = grid.len();
        let n = grid[0].len();
        let total = m * n;
        let mut parent = (0..total).collect::<Vec<_>>();
        let mut rank = vec![0; total];

        /// 查找根节点（路径压缩，迭代版）
        fn find(parent: &mut Vec<usize>, mut x: usize) -> usize {
            while parent[x] != x {
                parent[x] = parent[parent[x]];
                x = parent[x];
            }
            x
        }

        /// 合并两个集合，若已在同一集合则返回 false（表示遇到环）
        #[inline]
        fn union(parent: &mut Vec<usize>, rank: &mut Vec<usize>, a: usize, b: usize) -> bool {
            let ra = find(parent, a);
            let rb = find(parent, b);
            if ra == rb {
                return false;
            }
            // 按秩合并
            if rank[ra] < rank[rb] {
                parent[ra] = rb;
            } else if rank[ra] > rank[rb] {
                parent[rb] = ra;
            } else {
                parent[rb] = ra;
                rank[ra] += 1;
            }
            true
        }

        // 只检查向右和向下的边，避免重复
        for i in 0..m {
            for j in 0..n {
                let idx = i * n + j;
                // 检查右侧邻居
                if j + 1 < n && grid[i][j] == grid[i][j + 1] {
                    if !union(&mut parent, &mut rank, idx, idx + 1) {
                        return true;
                    }
                }
                // 检查下方邻居
                if i + 1 < m && grid[i][j] == grid[i + 1][j] {
                    if !union(&mut parent, &mut rank, idx, idx + n) {
                        return true;
                    }
                }
            }
        }
        false
    }
}
```
