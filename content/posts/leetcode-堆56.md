---
title: "leetcode-堆56"
date: 2026-08-21T09:54:14+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 购买物品的最大开销

给你一个下标从 0 开始大小为 m * n 的整数矩阵 values ，表示 m 个不同商店里 m * n 件不同的物品。每个商店有 n 件物品，第 i 个商店的第 j 件物品的价值为 values[i][j] 。除此以外，第 i 个商店的物品已经按照价值非递增排好序了，也就是说对于所有 0 <= j < n - 1 都有 values[i][j] >= values[i][j + 1] 。

每一天，你可以在一个商店里购买一件物品。具体来说，在第 d 天，你可以：

选择商店 i 。
购买数组中最右边的物品 j ，开销为 values[i][j] * d 。换句话说，选择该商店中还没购买过的物品中最大的下标 j ，并且花费 values[i][j] * d 去购买。

注意，所有物品都视为不同的物品。比方说如果你已经从商店 1 购买了物品 0 ，你还可以在别的商店里购买其他商店的物品 0 。

请你返回购买所有 m * n 件物品需要的 最大开销 。


```
use std::cmp::Reverse;

impl Solution {
    pub fn max_spending(values: Vec<Vec<i32>>) -> i64 {
        let m = values.len();
        let n = values[0].len();

        // 小根堆：每天从各商店最右物品中选价值最小的
        let mut heap = std::collections::BinaryHeap::with_capacity(m);
        for i in 0..m {
            heap.push(Reverse((values[i][n - 1], i, n - 1)));
        }

        let mut ans = 0i64;
        for day in 1..=m * n {
            let Reverse((val, i, j)) = heap.pop().unwrap();
            ans += (val as i64) * (day as i64);
            if j > 0 {
                heap.push(Reverse((values[i][j - 1], i, j - 1)));
            }
        }
        ans
    }
}
```
