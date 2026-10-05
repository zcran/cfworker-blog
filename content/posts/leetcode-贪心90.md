---
title: "leetcode-贪心90"
date: 2026-10-03T12:55:40+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 网格图中机器人回家的最小代价

给你一个 m x n 的网格图，其中 (0, 0) 是最左上角的格子，(m - 1, n - 1) 是最右下角的格子。给你一个整数数组 startPos ，startPos = [startrow, startcol] 表示 初始 有一个 机器人 在格子 (startrow, startcol) 处。同时给你一个整数数组 homePos ，homePos = [homerow, homecol] 表示机器人的 家 在格子 (homerow, homecol) 处。

机器人需要回家。每一步它可以往四个方向移动：上，下，左，右，同时机器人不能移出边界。每一步移动都有一定代价。再给你两个下标从 0 开始的整数数组：长度为 m 的数组 rowCosts  和长度为 n 的数组 colCosts 。

如果机器人往 上 或者往 下 移动到第 r 行 的格子，那么代价为 rowCosts[r] 。
如果机器人往 左 或者往 右 移动到第 c 列 的格子，那么代价为 colCosts[c] 。

请你返回机器人回家需要的 最小总代价 。


```
impl Solution {
    pub fn min_cost(start_pos: Vec<i32>, home_pos: Vec<i32>, row_costs: Vec<i32>, col_costs: Vec<i32>) -> i32 {
        let (r1, c1) = (start_pos[0] as usize, start_pos[1] as usize);
        let (r2, c2) = (home_pos[0] as usize, home_pos[1] as usize);

        let mut cost = 0;

        // 行：从 r1 走到 r2，每次移动支付目标行的代价
        // 若 r1 < r2，支付 rowCosts[r1+1 ..= r2]；若 r1 > r2，支付 rowCosts[r2 .. r1]
        if r1 < r2 {
            for r in (r1 + 1)..=r2 {
                cost += row_costs[r];
            }
        } else if r1 > r2 {
            for r in r2..r1 {
                cost += row_costs[r];
            }
        }

        // 列：从 c1 走到 c2，每次移动支付目标列的代价
        if c1 < c2 {
            for c in (c1 + 1)..=c2 {
                cost += col_costs[c];
            }
        } else if c1 > c2 {
            for c in c2..c1 {
                cost += col_costs[c];
            }
        }

        cost
    }
}
```
