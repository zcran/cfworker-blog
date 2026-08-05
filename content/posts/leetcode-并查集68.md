---
title: "leetcode-并查集68"
date: 2026-06-25T11:22:24+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 省份数量

有 n 个城市，其中一些彼此相连，另一些没有相连。如果城市 a 与城市 b 直接相连，且城市 b 与城市 c 直接相连，那么城市 a 与城市 c 间接相连。

省份 是一组直接或间接相连的城市，组内不含其他没有相连的城市。

给你一个 n x n 的矩阵 isConnected ，其中 isConnected[i][j] = 1 表示第 i 个城市和第 j 个城市直接相连，而 isConnected[i][j] = 0 表示二者不直接相连。

返回矩阵中 省份 的数量。


```
impl Solution {
    /// 使用并查集统计省份数量
    pub fn find_circle_num(is_connected: Vec<Vec<i32>>) -> i32 {
        let n = is_connected.len();
        let mut parent: Vec<usize> = (0..n).collect();
        let mut size = vec![1; n];
        let mut provinces = n as i32;

        /// 路径压缩（迭代版）
        fn find(parent: &mut Vec<usize>, mut x: usize) -> usize {
            while parent[x] != x {
                parent[x] = parent[parent[x]];
                x = parent[x];
            }
            x
        }

        /// 按大小合并
        fn union(parent: &mut Vec<usize>, size: &mut Vec<usize>, a: usize, b: usize) -> bool {
            let ra = find(parent, a);
            let rb = find(parent, b);
            if ra == rb {
                return false;
            }
            if size[ra] < size[rb] {
                parent[ra] = rb;
                size[rb] += size[ra];
            } else {
                parent[rb] = ra;
                size[ra] += size[rb];
            }
            true
        }

        // 遍历上三角矩阵，只需处理一次每条边
        for i in 0..n {
            for j in i + 1..n {
                if is_connected[i][j] == 1 && union(&mut parent, &mut size, i, j) {
                    provinces -= 1;
                }
            }
        }

        provinces
    }
}
```
