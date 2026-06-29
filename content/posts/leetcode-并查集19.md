---
title: "leetcode-并查集19"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 由斜杠划分区域


在由 1 x 1 方格组成的 n x n 网格 grid 中，每个 1 x 1 方块由 '/'、'\' 或空格构成。这些字符会将方块划分为一些共边的区域。

给定网格 grid 表示为一个字符串数组，返回 区域的数量 。

请注意，反斜杠字符是转义的，因此 '\' 用 '\\' 表示。

```
impl Solution {
    /// 返回由斜杠划分的区域数量。
    /// 将每个格子划分为 4 个三角形（上、右、下、左），
    /// 根据斜杠类型合并内部三角形，并合并相邻格子的三角形。
    /// 最后并查集的连通分量数即为区域数（外部区域也算一个分量）。
    pub fn regions_by_slashes(grid: Vec<String>) -> i32 {
        let n = grid.len();
        let total = n * n * 4; // 每个格子 4 个三角形
        let mut parent = (0..total).collect::<Vec<_>>();
        let mut rank = vec![0; total];
        let mut count = total as i32;

        // 并查集查找（路径压缩）
        fn find(parent: &mut Vec<usize>, mut x: usize) -> usize {
            let mut root = x;
            while parent[root] != root {
                root = parent[root];
            }
            while parent[x] != x {
                let next = parent[x];
                parent[x] = root;
                x = next;
            }
            root
        }

        // 并查集合并（按秩合并）
        #[inline]
        fn union(parent: &mut Vec<usize>, rank: &mut Vec<usize>, count: &mut i32, a: usize, b: usize) {
            let ra = find(parent, a);
            let rb = find(parent, b);
            if ra != rb {
                if rank[ra] < rank[rb] {
                    parent[ra] = rb;
                } else if rank[ra] > rank[rb] {
                    parent[rb] = ra;
                } else {
                    parent[rb] = ra;
                    rank[ra] += 1;
                }
                *count -= 1;
            }
        }

        for i in 0..n {
            let row = grid[i].as_bytes();
            for j in 0..n {
                let base = (i * n + j) * 4; // 当前格子的起始索引
                let ch = row[j];

                // 格子内部三角形合并
                match ch {
                    b'/' => {
                        // '/' 连接左上-右下，合并上(0)与左(3)，下(2)与右(1)
                        union(&mut parent, &mut rank, &mut count, base, base + 3);
                        union(&mut parent, &mut rank, &mut count, base + 1, base + 2);
                    }
                    b'\\' => {
                        // '\\' 连接右上-左下，合并上(0)与右(1)，下(2)与左(3)
                        union(&mut parent, &mut rank, &mut count, base, base + 1);
                        union(&mut parent, &mut rank, &mut count, base + 2, base + 3);
                    }
                    _ => {
                        // 空格：四个三角形全部连通
                        union(&mut parent, &mut rank, &mut count, base, base + 1);
                        union(&mut parent, &mut rank, &mut count, base + 1, base + 2);
                        union(&mut parent, &mut rank, &mut count, base + 2, base + 3);
                    }
                }

                // 与右侧格子合并：当前右三角形(1) ↔ 右侧左三角形(3)
                if j + 1 < n {
                    let right_base = (i * n + j + 1) * 4;
                    union(&mut parent, &mut rank, &mut count, base + 1, right_base + 3);
                }
                // 与下方格子合并：当前下三角形(2) ↔ 下方上三角形(0)
                if i + 1 < n {
                    let down_base = ((i + 1) * n + j) * 4;
                    union(&mut parent, &mut rank, &mut count, base + 2, down_base);
                }
            }
        }

        count // 连通分量数即为区域数
    }
}
```
