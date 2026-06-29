---
title: "leetcode-并查集4"
date: 2026-06-25T11:22:21+08:00
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
    pub fn find_circle_num(is_connected: Vec<Vec<i32>>) -> i32 {
        let n = is_connected.len();
        let mut parent = (0..n).collect::<Vec<_>>();

        /// 查找根节点，带路径压缩
        fn find(parent: &mut [usize], mut x: usize) -> usize {
            while parent[x] != x {
                parent[x] = parent[parent[x]]; // 路径压缩
                x = parent[x];
            }
            x
        }

        /// 合并两个集合
        fn union(parent: &mut [usize], x: usize, y: usize) {
            let rx = find(parent, x);
            let ry = find(parent, y);
            if rx != ry {
                parent[rx] = ry;
            }
        }

        // 遍历上三角矩阵，合并直接相连的城市
        for i in 0..n {
            for j in i + 1..n {
                if is_connected[i][j] == 1 {
                    union(&mut parent, i, j);
                }
            }
        }

        // 统计根节点数量即为省份数
        let mut provinces = 0;
        for i in 0..n {
            if find(&mut parent, i) == i {
                provinces += 1;
            }
        }
        provinces
    }
}
```
