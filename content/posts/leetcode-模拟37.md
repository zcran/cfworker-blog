---
title: "leetcode-模拟37"
date: 2026-08-08T11:31:10+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 统计不开心的朋友

给你一份 n 位朋友的亲近程度列表，其中 n 总是 偶数 。

对每位朋友 i，preferences[i] 包含一份 按亲近程度从高到低排列 的朋友列表。换句话说，排在列表前面的朋友与 i 的亲近程度比排在列表后面的朋友更高。每个列表中的朋友均以 0 到 n-1 之间的整数表示。

所有的朋友被分成几对，配对情况以列表 pairs 给出，其中 pairs[i] = [xi, yi] 表示 xi 与 yi 配对，且 yi 与 xi 配对。

但是，这样的配对情况可能会使其中部分朋友感到不开心。在 x 与 y 配对且 u 与 v 配对的情况下，如果同时满足下述两个条件，x 就会不开心：

x 与 u 的亲近程度胜过 x 与 y，且
u 与 x 的亲近程度胜过 u 与 v

返回 不开心的朋友的数目 。


```
use std::collections::HashSet;

impl Solution {
    pub fn unhappy_friends(n: i32, preferences: Vec<Vec<i32>>, pairs: Vec<Vec<i32>>) -> i32 {
        let n = n as usize;

        // 构建配对关系：partner[x] = y 表示 x 的配对对象是 y
        let mut partner = vec![0; n];
        for p in &pairs {
            partner[p[0] as usize] = p[1] as usize;
            partner[p[1] as usize] = p[0] as usize;
        }

        // 构建偏好排名：rank[x][y] = k 表示 y 在 x 的偏好列表中排第 k 位（越小越亲近）
        let mut rank = vec![vec![0; n]; n];
        for (x, pref) in preferences.iter().enumerate() {
            for (k, &y) in pref.iter().enumerate() {
                rank[x][y as usize] = k;
            }
        }

        let mut unhappy = 0;

        for x in 0..n {
            let y = partner[x];
            let rank_xy = rank[x][y];

            // 检查是否存在 u，使得 x 更亲近 u，且 u 也更亲近 x
            for &u in &preferences[x] {
                let u = u as usize;
                if rank[x][u] > rank_xy {
                    break; // u 的亲近度已低于当前配对 y，后续更不可能
                }
                let v = partner[u];
                if rank[u][x] < rank[u][v] {
                    unhappy += 1;
                    break; // x 已确认不开心，无需继续检查
                }
            }
        }

        unhappy
    }
}
```
