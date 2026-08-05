---
title: "leetcode-图33"
date: 2026-07-27T10:01:37+08:00
tags: ["leetcode", "图"]
draft: false
---


##  按距离统计房屋对数目 I

给你三个 正整数 n 、x 和 y 。

在城市中，存在编号从 1 到 n 的房屋，由 n 条街道相连。对所有 1 <= i < n ，都存在一条街道连接编号为 i 的房屋与编号为 i + 1 的房屋。另存在一条街道连接编号为 x 的房屋与编号为 y 的房屋。

对于每个 k（1 <= k <= n），你需要找出所有满足要求的 房屋对 [house1, house2] ，即从 house1 到 house2 需要经过的 最少 街道数为 k 。

返回一个下标从 1 开始且长度为 n 的数组 result ，其中 result[k] 表示所有满足要求的房屋对的数量，即从一个房屋到另一个房屋需要经过的 最少 街道数为 k 。

注意，x 与 y 可以 相等 。




```
impl Solution {
    pub fn count_of_pairs(n: i32, x: i32, y: i32) -> Vec<i32> {
        let n = n as usize;
        let (x, y) = (x as usize - 1, y as usize - 1);

        // 初始化距离矩阵：线性街道的初始距离
        let mut dist = vec![vec![0; n]; n];
        for i in 0..n {
            for j in i..n {
                let d = (j - i) as i32;
                dist[i][j] = d;
                dist[j][i] = d;
            }
        }

        // 利用额外边 (x, y) 优化距离（类似 Floyd 单边松弛）
        for i in 0..n {
            for j in 0..n {
                let via_xy = dist[i][x] + 1 + dist[y][j];
                if via_xy < dist[i][j] {
                    dist[i][j] = via_xy;
                    dist[j][i] = via_xy;
                }
            }
        }

        // 统计每个距离的出现次数
        let mut result = vec![0; n];
        for i in 0..n {
            for j in 0..n {
                if i != j {
                    let d = dist[i][j] as usize;
                    if d <= n {
                        result[d - 1] += 1;
                    }
                }
            }
        }

        result
    }
}
```
