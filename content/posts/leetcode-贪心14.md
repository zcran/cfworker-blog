---
title: "leetcode-贪心14"
date: 2026-10-03T12:55:34+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 超级洗衣机

假设有 n 台超级洗衣机放在同一排上。开始的时候，每台洗衣机内可能有一定量的衣服，也可能是空的。

在每一步操作中，你可以选择任意 m (1 <= m <= n) 台洗衣机，与此同时将每台洗衣机的一件衣服送到相邻的一台洗衣机。

给定一个整数数组 machines 代表从左至右每台洗衣机中的衣物数量，请给出能让所有洗衣机中剩下的衣物的数量相等的 最少的操作步数 。如果不能使每台洗衣机中衣物的数量相等，则返回 -1 。


```
impl Solution {
    pub fn find_min_moves(machines: Vec<i32>) -> i32 {
        let n = machines.len() as i32;
        let sum: i32 = machines.iter().sum();

        // 衣服总数无法均分，直接无解
        if sum % n != 0 {
            return -1;
        }

        let avg = sum / n;
        // prefix: 前缀净盈余（正数表示左边多出的衣服需向右运，负数反之）
        // 每一步操作的下界由两部分决定：
        //   1. |prefix|：前缀整体与外界的净搬运量
        //   2. cur_diff：单台洗衣机一次最多只能送出一件，其自身盈余需逐步分摊
        let (mut prefix, mut ans) = (0, 0);

        for &num in &machines {
            let cur_diff = num - avg;
            prefix += cur_diff;
            ans = ans.max(prefix.abs()).max(cur_diff);
        }

        ans
    }
}
```
