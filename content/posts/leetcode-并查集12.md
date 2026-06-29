---
title: "leetcode-并查集12"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 打砖块

有一个 m x n 的二元网格 grid ，其中 1 表示砖块，0 表示空白。砖块 稳定（不会掉落）的前提是：

· 一块砖直接连接到网格的顶部，或者
· 至少有一块相邻（4 个方向之一）砖块 稳定 不会掉落时

给你一个数组 hits ，这是需要依次消除砖块的位置。每当消除 hits[i] = (rowi, coli) 位置上的砖块时，对应位置的砖块（若存在）会消失，然后其他的砖块可能因为这一消除操作而 掉落 。一旦砖块掉落，它会 立即 从网格 grid 中消失（即，它不会落在其他稳定的砖块上）。

返回一个数组 result ，其中 result[i] 表示第 i 次消除操作对应掉落的砖块数目。

注意，消除可能指向是没有砖块的空白位置，如果发生这种情况，则没有砖块掉落。


```
impl Solution {
    pub fn hit_bricks(grid: Vec<Vec<i32>>, hits: Vec<Vec<i32>>) -> Vec<i32> {
        let rows = grid.len();
        let cols = grid[0].len();
        let total = rows * cols;
        let root = total; // 虚拟根节点，连接所有顶部砖块

        // 逆向构建：从最终状态开始反向添加砖块
        let mut after = grid.clone();
        for hit in &hits {
            after[hit[0] as usize][hit[1] as usize] = 0;
        }

        let mut parent = (0..=total).collect::<Vec<_>>();
        let mut size = vec![1; total + 1];
        size[root] = 0; // 虚拟根节点不计入大小

        /// 查找根节点，带路径压缩
        fn find(parent: &mut [usize], mut x: usize) -> usize {
            while parent[x] != x {
                parent[x] = parent[parent[x]];
                x = parent[x];
            }
            x
        }

        /// 合并两个集合，返回是否合并成功
        fn union(parent: &mut [usize], size: &mut [i32], root: usize, x: usize, y: usize) -> bool {
            let rx = find(parent, x);
            let ry = find(parent, y);
            if rx == ry {
                return false;
            }
            // 总是将非根节点合并到根节点或更大的集合
            if rx == root || (ry != root && size[rx] >= size[ry]) {
                parent[ry] = rx;
                size[rx] += size[ry];
            } else {
                parent[rx] = ry;
                size[ry] += size[rx];
            }
            true
        }

        // 初始化并查集：连接所有稳定砖块
        // 1. 连接顶部砖块到虚拟根节点
        for j in 0..cols {
            if after[0][j] == 1 {
                union(&mut parent, &mut size, root, j, root);
            }
        }
        // 2. 连接内部相邻砖块
        for i in 1..rows {
            for j in 0..cols {
                if after[i][j] == 0 {
                    continue;
                }
                let idx = i * cols + j;
                // 与上方砖块连接
                if after[i - 1][j] == 1 {
                    union(&mut parent, &mut size, root, idx, (i - 1) * cols + j);
                }
                // 与左侧砖块连接（避免重复）
                if j > 0 && after[i][j - 1] == 1 {
                    union(&mut parent, &mut size, root, idx, i * cols + j - 1);
                }
            }
        }

        // 记录每次操作后的稳定砖块数量（逆向）
        let mut stable_counts = vec![size[root]];
        let dirs = [(1, 0), (-1, 0), (0, 1), (0, -1)];

        // 逆向处理 hits
        for hit in hits.iter().rev() {
            let (i, j) = (hit[0] as usize, hit[1] as usize);

            // 如果原位置没有砖块，稳定数量不变
            if grid[i][j] == 0 {
                stable_counts.push(size[root]);
                continue;
            }

            // 恢复砖块
            after[i][j] = 1;
            let idx = i * cols + j;

            // 连接相邻的砖块
            for &(dx, dy) in &dirs {
                let ni = i as isize + dx;
                let nj = j as isize + dy;
                if ni >= 0 && ni < rows as isize && nj >= 0 && nj < cols as isize {
                    let ni = ni as usize;
                    let nj = nj as usize;
                    if after[ni][nj] == 1 {
                        union(&mut parent, &mut size, root, idx, ni * cols + nj);
                    }
                }
            }

            // 如果砖块在顶部，连接到虚拟根节点
            if i == 0 {
                union(&mut parent, &mut size, root, idx, root);
            }

            stable_counts.push(size[root]);
        }

        // 计算每次消除掉落的砖块数量
        let mut result = vec![0; hits.len()];
        for k in 0..hits.len() {
            // 稳定砖块增加的数量 - 1（当前恢复的砖块本身）
            let dropped = stable_counts[hits.len() - k] - stable_counts[hits.len() - k - 1] - 1;
            result[k] = dropped.max(0);
        }

        result
    }
}
```
