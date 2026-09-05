---
title: "leetcode-堆14"
date: 2026-08-21T09:54:11+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 最后一块石头的重量


有一堆石头，每块石头的重量都是正整数。

每一回合，从中选出两块 最重的 石头，然后将它们一起粉碎。假设石头的重量分别为 x 和 y，且 x <= y。那么粉碎的可能结果如下：

如果 x == y，那么两块石头都会被完全粉碎；
如果 x != y，那么重量为 x 的石头将会完全粉碎，而重量为 y 的石头新重量为 y-x。

最后，最多只会剩下一块石头。返回此石头的重量。如果没有石头剩下，就返回 0。



```
use std::collections::BinaryHeap;

impl Solution {
    /// 每次取两块最重的石头粉碎，用最大堆维护石头重量
    pub fn last_stone_weight(stones: Vec<i32>) -> i32 {
        let mut heap = BinaryHeap::from(stones);

        while heap.len() > 1 {
            let y = heap.pop().unwrap(); // 最重的
            let x = heap.pop().unwrap(); // 次重的
            if y != x {
                heap.push(y - x); // 剩余重量放回堆
            }
        }

        heap.pop().unwrap_or(0)
    }
}

```
