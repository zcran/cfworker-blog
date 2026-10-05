---
title: "leetcode-二分查找51"
date: 2026-09-28T12:29:13+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 分配给商店的最多商品的最小值

给你一个整数 n ，表示有 n 间零售商店。总共有 m 种商品，每种商品的数目用一个下标从 0 开始的整数数组 quantities 表示，其中 quantities[i] 表示第 i 种商品的数目。

你需要将 所有商品 分配到零售商店，并遵守这些规则：

一间商店 至多 只能有 一种商品 ，但一间商店拥有的商品数目可以为 任意 件。

分配后，每间商店都会被分配一定数目的商品（可能为 0 件）。用 x 表示所有商店中分配商品数目的最大值，你希望 x 越小越好。也就是说，你想 最小化 分配给任意商店商品数目的 最大值 。

请你返回最小的可能的 x 。


```
impl Solution {
    pub fn minimized_maximum(n: i32, quantities: Vec<i32>) -> i32 {
        // 二分答案：每间商店最多分配 mid 件商品时，判断需要的商店数是否 <= n
        let feasible = |mid: i32| -> bool {
            let mut shops = 0i64;
            for &q in &quantities {
                // 每种商品需要的商店数为 ceil(q / mid)
                shops += ((q - 1) / mid + 1) as i64;
                if shops > n as i64 {
                    return false;
                }
            }
            true
        };

        let mut lo = 1;
        let mut hi = *quantities.iter().max().unwrap();
        while lo < hi {
            let mid = lo + (hi - lo) / 2;
            if feasible(mid) {
                hi = mid;
            } else {
                lo = mid + 1;
            }
        }
        lo
    }
}
```
